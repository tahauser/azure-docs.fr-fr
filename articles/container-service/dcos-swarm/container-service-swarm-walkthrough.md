---
title: "Démarrage rapide - Azure Docker Swarm cluster pour Linux"
description: "Découvrez rapidement comment créer un cluster Docker Swarm pour des conteneurs Linux dans Azure Container Service, avec Azure CLI."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 691c09c8dfb5335aabb431d06bd687963fe4c488
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-docker-swarm-cluster"></a>Déployer le cluster Docker Swarm

Dans ce guide de démarrage rapide, un cluster Docker Swarm est déployé à l’aide d’Azure CLI. Une application à plusieurs conteneurs composée d’un serveur web frontal et d’une instance Redis est ensuite déployée, puis exécutée sur le cluster. Ceci fait, l’application est accessible via internet.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce guide de démarrage rapide nécessite que vous exécutiez Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Output:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westcentralus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Créer le cluster Docker Swarm

Pour créer un cluster Docker Swarm dans Azure Container Service, utilisez la commande [az acs create](/cli/azure/acs#az_acs_create). 

L’exemple ci-après permet de créer un cluster nommé *mySwarmCluster*, qui inclut un nœud maître Linux et trois nœuds agents Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type Swarm --resource-group myResourceGroup --generate-ssh-keys
```

Dans certains cas, par exemple avec une version d’évaluation limitée, un abonnement Azure dispose d’un accès limité aux ressources Azure. Si le déploiement échoue à cause d’une limitation du nombre de cœurs disponibles, réduisez le nombre d’agents par défaut en ajoutant `--agent-count 1` à la commande [az acs create](/cli/azure/acs#az_acs_create). 

Au bout de quelques minutes, la commande se termine et retourne des informations formatées Json sur le cluster.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Pour suivre ce guide de démarrage rapide, vous avez besoin de l’adresse IP du nœud maître Docker Swarm et du pool d’agents Docker. Exécutez la commande suivante pour retourner les deux adresses IP.


```bash
az network public-ip list --resource-group myResourceGroup --query "[*].{Name:name,IPAddress:ipAddress}" -o table
```

Output:

```bash
Name                                                                 IPAddress
-------------------------------------------------------------------  -------------
swarmm-agent-ip-myswarmcluster-myresourcegroup-d5b9d4agent-66066781  52.179.23.131
swarmm-master-ip-myswarmcluster-myresourcegroup-d5b9d4mgmt-66066781  52.141.37.199
```

Créez un tunnel SSH vers le nœud maître Docker Swarm. Remplacez `IPAddress` par l’adresse IP du nœud maître Docker Swarm.

```bash
ssh -p 2200 -fNL 2375:localhost:2375 azureuser@IPAddress
```

Définissez la variable d’environnement `DOCKER_HOST`. Ceci vous permet d’exécuter des commandes docker pour le Docker Swarm sans avoir à spécifier le nom de l’hôte.

```bash
export DOCKER_HOST=:2375
```

Vous êtes maintenant prêt à exécuter les services Docker sur le Docker Swarm.


## <a name="run-the-application"></a>Exécution de l'application

Créez un fichier nommé `docker-compose.yaml`, puis copiez-y le contenu suivant.

```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    container_name: azure-vote-back
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Exécutez la commande ci-dessous pour créer le service Azure Vote.

```bash
docker-compose up -d
```

Output:

```bash
Creating network "user_default" with the default driver
Pulling azure-vote-front (microsoft/azure-vote-front:v1)...
swarm-agent-EE873B23000005: Pulling microsoft/azure-vote-front:v1...
swarm-agent-EE873B23000004: Pulling microsoft/azure-vote-front:v1... : downloaded
Pulling azure-vote-back (redis:latest)...
swarm-agent-EE873B23000004: Pulling redis:latest... : downloaded
Creating azure-vote-front ... 
Creating azure-vote-back ... 
Creating azure-vote-front
Creating azure-vote-back ...
```

## <a name="test-the-application"></a>Test de l'application

Naviguez dans l’adresse IP du pool d’agents Swarm pour tester l’application Azure Vote.

![Image de la navigation vers Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Supprimer un cluster
Lorsque vous n’avez plus besoin du cluster, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le service de conteneur et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obtenir le code

Dans ce guide de démarrage rapide, des images de conteneur créées au préalable ont été utilisées pour créer un service Docker. Le code de l’application associé, Dockerfile, et le fichier Compose sont disponibles sur GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un cluster Docker Swarm et vous y avez déployé une application de plusieurs conteneurs.

Pour en savoir plus sur l’intégration de Docker Swarm avec Visual Studio Team Services, passez à la section CI/CD avec Docker Swarm et VSTS.

> [!div class="nextstepaction"]
> [CI/CD avec Docker Swarm et VSTS](./container-service-docker-swarm-setup-ci-cd.md)