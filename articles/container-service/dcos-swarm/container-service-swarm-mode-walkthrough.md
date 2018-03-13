---
title: "Guide de démarrage rapide - Cluster Azure Docker CE pour Linux"
description: "Découvrez rapidement comment créer un cluster Docker CE pour des conteneurs Linux dans Azure Container Service, avec Azure CLI."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: 
ms.openlocfilehash: 3c06aaa09366df89ad73cb60780511d2087d5994
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-docker-ce-cluster"></a>Déployer le cluster Docker CE

Dans ce guide de démarrage rapide, un cluster Docker CE est déployé à l’aide d’Azure CLI. Une application de plusieurs conteneurs composée d’un serveur web frontal et d’une instance Redis est déployée, puis exécutée sur le cluster. Ceci fait, l’application est accessible via internet.

Docker CE est en version préliminaire sur Azure Container Service et **ne doit pas être utilisé pour des charges de travail de production**.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un groupe logique dans lequel des ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *ukwest*.

```azurecli-interactive
az group create --name myResourceGroup --location ukwest
```

Output:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "ukwest",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Créer le cluster Docker Swarm

Pour créer un cluster Docker CE dans Azure Container Service, utilisez la commande [az acs create](/cli/azure/acs#az_acs_create). 

L’exemple ci-après permet de créer un cluster nommé *mySwarmCluster*, qui inclut un nœud maître Linux et trois nœuds agents Linux.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

Dans certains cas, par exemple avec une version d’évaluation limitée, un abonnement Azure dispose d’un accès limité aux ressources Azure. Si le déploiement échoue à cause d’une limitation du nombre de cœurs disponibles, réduisez le nombre d’agents par défaut en ajoutant `--agent-count 1` à la commande [az acs create](/cli/azure/acs#az_acs_create). 

Au bout de quelques minutes, la commande se termine et retourne des informations formatées JSON sur le cluster.

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Pour suivre ce guide de démarrage rapide, vous avez besoin du nom de domaine complet du nœud maître Docker Swarm et du pool d’agents Docker. Exécutez la commande suivante pour retourner les noms de domaine complets du nœud maître et de l’agent.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Output:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Créez un tunnel SSH vers le nœud maître Docker Swarm. Remplacez `MasterFQDN` par l’adresse du nom de domaine complet du nœud maître Docker Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Définissez la variable d’environnement `DOCKER_HOST`. Ceci vous permet d’exécuter des commandes docker pour le Docker Swarm sans avoir à spécifier le nom de l’hôte.

```bash
export DOCKER_HOST=localhost:2374
```

Vous êtes maintenant prêt à exécuter les services Docker sur le Docker Swarm.


## <a name="run-the-application"></a>Exécution de l'application

Créez un fichier nommé `azure-vote.yaml`, puis copiez-y le contenu suivant.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Exécutez la commande [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) pour créer le service Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Output:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Utilisez la commande [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) pour retourner l’état du déploiement de l’application.

```bash
docker stack ps azure-vote
```

L’application est prête lorsque la valeur `CURRENT STATE` de chaque service est `Running`.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Test de l'application

Naviguez dans le nom de domaine complet du pool d’agents Swarm pour tester l’application Azure Vote.

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
