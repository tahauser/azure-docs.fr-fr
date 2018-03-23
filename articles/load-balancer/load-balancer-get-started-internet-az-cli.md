---
title: Créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant dans une zone à l’aide d’Azure CLI | Microsoft Docs
description: Découvrez comment créer une instance publique de Load Balancer Standard avec un frontend d’adresse IP publique redondant dans une zone à l’aide d’Azure CLI.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: 725e1717110ebfe52e2b45ab0c45edc36055704f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Créer une instance publique de Load Balancer Standard avec un frontend redondant dans une zone à l’aide d’Azure CLI

Cet article décrit les étapes de création d’une instance publique de [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) avec un frontend redondant dans une zone qui utilise une adresse IP publique Standard.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>S’inscrire pour bénéficier de la préversion des zones de disponibilité, de Load Balancer Standard et des adresses IP publiques Standard

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.17 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel.  Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

>[!NOTE]
La [référence SKU de Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) est actuellement en préversion. Le niveau de disponibilité et la fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour vos services de production, utilisez la [référence SKU De base de Load Balancer](load-balancer-overview.md) de la version publique. 

> [!NOTE]
> Les zones de disponibilité sont en préversion et sont préparées pour vos scénarios de développement et de test. La prise en charge est fournie pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour bien démarrer avec les zones de disponibilité, et pour plus d’informations sur les ressources, les régions et les familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez tester les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Avant de sélectionner une zone ou une option de redondance dans une zone pour l’adresse IP publique frontend de Load Balancer, vous devez d’abord suivre les étapes de la section [S’inscrire à la préversion des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview).

Assurez-vous que vous avez installé la dernière version d’[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) et que vous êtes connecté à un compte Azure avec [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources à l’aide de la commande suivante :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Créer une adresse IP publique Standard

Créer une adresse IP publique Standard à l’aide de la commande suivante :

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Créer un équilibrage de charge

Créez une instance publique de Load Balancer Standard avec l’adresse IP publique standard que vous avez créée à l’étape précédente, à l’aide de la commande suivante :

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Créer une sonde d’équilibreur de charge sur le port 80

Créez une sonde d’intégrité d’équilibreur de charge à l’aide de la commande suivante :

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Créer une règle d’équilibreur de charge pour le port 80

Créez une règle d’équilibreur de charge à l’aide de la commande suivante :

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [créer une adresse IP publique dans une zone de disponibilité](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address).



