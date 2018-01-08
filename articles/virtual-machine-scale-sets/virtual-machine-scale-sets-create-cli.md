---
title: "Créer un groupe de machines virtuelles identiques avec Azure CLI 2.0 | Microsoft Docs"
description: "Apprendre à créer rapidement une machine virtuelle avec Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 222bfa1c3070fa4634cf5c48d934a6387c48a4b0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Créer un groupe de machines virtuelles identiques avec Azure CLI 2.0
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique ou définir des règles pour mettre à l’échelle automatiquement en fonction de l’utilisation des ressources (processeur, demande de mémoire ou trafic réseau). Dans cet article de prise en main, vous créez un groupe de machines virtuelles identiques avec Azure CLI 2.0. Vous pouvez également créer un groupe identique avec [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) ou le [portail Azure](virtual-machine-scale-sets-create-portal.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Créer un groupe identique
Pour pouvoir créer un groupe identique, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Créez à présent un groupe de machines virtuelles identiques avec [az vmss create](/cli/azure/vmss#create). L’exemple suivant crée un groupe identique nommé *myScaleSet*, et génère des clés SSH si elles n’existent pas :

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.


## <a name="install-nginx-webserver"></a>Installer un serveur Web NGINX
Pour tester votre groupe identique, utilisez l’extension de script personnalisé pour télécharger et exécuter un script qui installe NGINX sur les instances de machine virtuelle. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Pour plus d’informations, consultez [Vue d’ensemble de l’extension de script personnalisé](../virtual-machines/linux/extensions-customscript.md).

Appliquez l’extension de script personnalisé qui installe NGINX comme suit :

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Autoriser le trafic web
Pour autoriser le trafic à atteindre le serveur web, créez une règle d’équilibreur de charge avec [az network lb rule create](/cli/azure/network/lb/rule#create). L’exemple suivant crée une règle nommée *myLoadBalancerRuleWeb* :

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>Tester votre serveur web
Pour voir votre serveur web en action, obtenez l’adresse IP publique de votre équilibreur de charge avec [az network public-ip show](/cli/azure/network/public-ip#show). L’exemple suivant obtient l’adresse IP pour *myScaleSetLBPublicIP* qui a été créée dans le cadre du groupe identique :

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Saisissez l’adresse IP publique de l’équilibrage de charge dans un navigateur web. L’équilibreur de charge répartit le trafic vers l’une de vos instances de machine virtuelle, comme illustré dans l’exemple suivant :

![Page web par défaut dans NGINX](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées, comme suit :

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>étapes suivantes
Dans cet article de prise en main, vous avez créé un groupe identique de base et vous avez utilisé l’extension de script personnalisé afin d’installer un serveur web NGINX de base sur les instances de machine virtuelle. Pour une meilleure automatisation et évolutivité, développez votre groupe identique à l’aide des articles de procédures suivants :

- [Déployer votre application sur des groupes de machines virtuelles identiques](virtual-machine-scale-sets-deploy-app.md)
- Faites une mise à l’échelle automatique avec [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), ou le [portail Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Utiliser les mises à niveau automatique du système d’exploitation pour les instances de machine virtuelle de votre groupe identique](virtual-machine-scale-sets-automatic-upgrade.md)