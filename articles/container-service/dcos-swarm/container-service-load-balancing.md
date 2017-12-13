---
title: "Équilibrer la charge des conteneurs dans un cluster DC/OS Azure"
description: "Équilibrer la charge de plusieurs conteneurs dans un cluster DC/OS Azure Container Service."
services: container-service
author: rgardler
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8dfa99b609cf2c54d29c55d3f5ba748910f7513b
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Équilibrer la charge des conteneurs dans un cluster DC/OS Azure Container Service

Dans cet article, nous étudions comment créer un équilibreur de charge interne dans un cluster Azure Container Service géré par DC/OS à l’aide de Marathon-LB. Cette configuration vous permet de mettre à l’échelle vos applications horizontalement. Elle vous permet également de tirer parti des clusters d’agents publics et privés en plaçant vos équilibreurs de charge sur le cluster public et vos conteneurs d’applications sur le cluster privé. Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer un équilibreur de charge Marathon
> * Déployer une application à l’aide de l’équilibreur de charge
> * Configurer un équilibreur de charge Azure

Vous avez besoin d’un cluster DC/OS ACS pour suivre les étapes de ce didacticiel. Le cas échéant, cet [exemple de script](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) peut en créer un pour vous.

Ce didacticiel requiert Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Vue d’ensemble de l’équilibrage de charge

Il existe deux couches d’équilibrage de charge dans un cluster DC/OS Azure Container Service : 

**Azure Load Balancer** fournit des points d’entrée publics (ceux auxquels les utilisateurs finaux ont accès). Un équilibreur de charge Azure Load Balancer est automatiquement fourni par Azure Container Service. Par défaut, il est configuré pour exposer les ports 80, 443 et 8080.

L’outil **Marathon Load Balancer (marathon-lb)** achemine les demandes entrantes vers les instances de conteneur qui traitent ces demandes. L’outil marathon-lb s’adapte dynamiquement au fur et à mesure que les conteneurs fournissant notre service web sont mis à l’échelle. Par défaut, cet équilibreur de charge n’est pas fourni dans votre service Container Service, mais il est facile à installer.

## <a name="configure-marathon-load-balancer"></a>Configurer un équilibreur de charge Marathon

L’équilibreur de charge Marathon se reconfigure dynamiquement en fonction des conteneurs que vous avez déployés. Il est également résistant à la perte d’un conteneur ou d’un agent. Le cas échéant, Apache Mesos redémarre le conteneur ailleurs et l’outil marathon-lb s’adapte.

Exécutez la commande suivante pour installer l’équilibreur de charge marathon sur le cluster de l’agent public.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Déployer une application à charge équilibrée

Maintenant que nous avons le package marathon-lb, nous pouvons déployer le conteneur d’applications dont nous souhaitons équilibrer la charge. 

Tout d’abord, obtenez le nom de domaine complet des agents exposés publiquement.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Ensuite, créez un fichier nommé *hello-web.json* et copiez-y le contenu suivant. L’étiquette `HAPROXY_0_VHOST` doit être mise à jour avec le nom de domaine complet des agents DC/OS. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Utilisez l’interface de ligne de commande DC/OS pour exécuter l’application. Par défaut, Marathon déploie l’application dans le cluster privé. Cela signifie que le déploiement ci-dessus est uniquement accessible via votre équilibreur de charge, ce qui correspond généralement au comportement souhaité.

```azurecli-interactive
dcos marathon app add hello-web.json
```

Une fois que l’application a été déployée, recherchez le nom de domaine complet du cluster d’agents pour afficher l’application à charge équilibrée.

![Image de l’application à charge équilibrée](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Configurer Azure Load Balancer

Par défaut, Azure Load Balancer expose les ports 80, 8080 et 443. Si vous utilisez l’un de ces trois ports (comme nous le faisons dans l’exemple ci-dessus), vous n’avez rien à faire. Vous devez pouvoir accéder au nom de domaine complet de l’équilibreur de charge de votre agent. À chaque actualisation, vous accéderez à l’un de vos trois serveurs web selon le principe du tourniquet (round robin). 

Si vous utilisez un port différent, vous devez ajouter une règle de tourniquet et une sonde sur l’équilibreur de charge pour le port que vous avez utilisé. Vous pouvez le faire depuis [l’interface de ligne de commande (CLI) Azure](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), avec les commandes `azure network lb rule create` et `azure network lb probe create`.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez découvert l’équilibrage de charge dans ACS avec les équilibreurs de charge Marathon et Azure, y compris les actions suivantes :

> [!div class="checklist"]
> * Configurer un équilibreur de charge Marathon
> * Déployer une application à l’aide de l’équilibreur de charge
> * Configurer un équilibreur de charge Azure

Passez au didacticiel suivant pour en savoir plus sur l’intégration du stockage Azure à DC/OS dans Azure.

> [!div class="nextstepaction"]
> [Monter un partage de fichiers Azure dans un cluster DC/OS](container-service-dcos-fileshare.md)