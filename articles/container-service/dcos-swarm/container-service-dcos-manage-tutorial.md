---
title: "Didacticiel Azure Container Service - Gérer DC/OS"
description: "Didacticiel Azure Container Service - Gérer DC/OS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ea5cbcc115e3da08b54d34da8b9d240c6d6a869c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="azure-container-service-tutorial---manage-dcos"></a>Didacticiel Azure Container Service - Gérer DC/OS

DC/OS propose une plateforme distribuée destinée à exécuter des applications en conteneur modernes. Avec Azure Container Service, l’approvisionnement d’un cluster DC/OS prêt pour la production est une opération simple et rapide. Ce démarrage rapide décrit en détail les étapes de base à suivre pour déployer un cluster DC/OS et exécuter une charge de travail de base.

> [!div class="checklist"]
> * Créer un cluster ACS DC/OS
> * Connexion au cluster
> * Installer l’interface de ligne de commande DC/OS
> * Déployer une application sur le cluster
> * Mettre à l’échelle une application sur le cluster
> * Mettre à l’échelle les nœuds d’un cluster DC/OS
> * Gestion DC/OS de base
> * Supprimer le cluster DC/OS

Ce didacticiel requiert Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Créer un cluster DC/OS

Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Ensuite, créez un cluster DC/OS avec la commande [az acs create](/cli/azure/acs#az_acs_create).

L’exemple suivant crée un cluster DC/OS nommé *myDCOSCluster* et crée des clés SSH si elles n’existent pas déjà. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

Au bout de quelques minutes, la commande se termine et retourne des informations sur le déploiement.

## <a name="connect-to-dcos-cluster"></a>Se connecter à un cluster DC/OS

Dès lors qu’un cluster DC/OS est créé, il est accessible via un tunnel SSH. Exécutez la commande suivante pour retourner l’adresse IP du maître DC/OS. Cette adresse IP est stockée dans une variable et utilisée à l’étape suivante.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Pour créer le tunnel SSH, exécutez la commande suivante et suivez les instructions qui s’affichent à l’écran. Si le port 80 est déjà utilisé, la commande échoue. Remplacez le port tunnelisé par un port non utilisé, tel que `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

## <a name="install-dcos-cli"></a>Installer l’interface de ligne de commande DC/OS

Installez l’interface CLI DC/OS en utilisant la commande [az acs dcos install-cli](/azure/acs/dcos#install-cli). Si vous utilisez Azure CloudShell, l’interface CLI DC/OS est déjà installée. Si vous exécutez Azure CLI sur macOS ou Linux, vous devrez peut-être exécuter la commande avec sudo.

```azurecli
az acs dcos install-cli
```

Avant de pouvoir être utilisée avec le cluster, l’interface CLI doit être configurée pour utiliser le tunnel SSH. Pour ce faire, exécutez la commande suivante en modifiant le port, si nécessaire.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Exécuter une application

Le mécanisme de planification par défaut d’un cluster ACS DC/OS est Marathon. Marathon permet de démarrer une application et de gérer son état sur le cluster DC/OS. Pour planifier une application via Marathon, créez un fichier sous le nom **marathon-app.json** et copiez-y le contenu suivant. 

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Exécutez la commande suivante pour planifier l’exécution de l’application sur le cluster DC/OS.

```azurecli
dcos marathon app add marathon-app.json
```

Pour afficher l’état de déploiement de l’application, exécutez la commande suivante.

```azurecli
dcos marathon app list
```

Quand la valeur de la colonne **TASKS** passe de *0/1* à *1/1*, le déploiement de l’application est terminé.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Mettre à l’échelle l’application Marathon

Dans l’exemple précédent, l’application qui à été créée présentait une seule instance. Pour mettre à jour ce déploiement de sorte que l’application ait trois instances disponibles, ouvrez le fichier **marathon-app.json** et faites passer la valeur de la propriété d’instance à 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Mettez à jour l’application à l’aide de la commande `dcos marathon app update`.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Pour afficher l’état de déploiement de l’application, exécutez la commande suivante.

```azurecli
dcos marathon app list
```

Quand la valeur de la colonne **TASKS** passe de *1/3* à *3/1*, le déploiement de l’application est terminé.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Exécuter l’application accessible par Internet

Le cluster ACS DC/OS se compose de deux ensembles de nœuds : un public qui est accessible sur Internet, et l’autre privé qui n’est pas accessible sur Internet. L’ensemble par défaut est celui qui est constitué des nœuds privés (celui qui a été utilisé dans le dernier exemple).

Pour rendre une application accessible sur Internet, déployez-la dans l’ensemble de nœuds public. Pour ce faire, attribuez à l’objet `acceptedResourceRoles` la valeur `slave_public`.

Créez un fichier sous le nom **nginx-public.json** et copiez-y le contenu suivant.

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Exécutez la commande suivante pour planifier l’exécution de l’application sur le cluster DC/OS.

```azurecli 
dcos marathon app add nginx-public.json
```

Procurez-vous l’adresse IP publique des agents du cluster public DC/OS.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Le site NGINX par défaut vous est retourné dès lors que vous accédez à cette adresse.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Mettre à l’échelle un cluster DC/OS

Dans les exemples précédents, une application a été mise à l’échelle pour présenter plusieurs instances. L’infrastructure DC/OS peut aussi être mise à l’échelle pour fournir plus ou moins de capacité de calcul. Cette opération s’effectue via la commande [az acs scale](). 

Pour afficher le nombre actuel d’agents DC/OS, utilisez la commande [az acs show](/cli/azure/acs#az_acs_show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Pour faire passer le nombre à 5, utilisez la commande [acs az scale](/cli/azure/acs#az_acs_scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Supprimer un cluster DC/OS

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le cluster DC/OS et toutes les ressources associées.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a permis de vous familiariser avec certaines tâches de gestion DC/OS de base, qui sont répertoriées ci-dessous. 

> [!div class="checklist"]
> * Créer un cluster ACS DC/OS
> * Connexion au cluster
> * Installer l’interface de ligne de commande DC/OS
> * Déployer une application sur le cluster
> * Mettre à l’échelle une application sur le cluster
> * Mettre à l’échelle les nœuds d’un cluster DC/OS
> * Supprimer le cluster DC/OS

Passez au didacticiel suivant pour découvrir l’application d’équilibrage de charge de DC/OS dans Azure. 

> [!div class="nextstepaction"]
> [Équilibrer la charge des applications](container-service-load-balancing.md)