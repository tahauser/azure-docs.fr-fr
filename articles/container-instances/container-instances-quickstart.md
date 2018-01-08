---
title: "Guide de démarrage rapide : créer son premier conteneur Azure Container Instances"
description: "Déploiement et prise en main d’Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: bf511f60a431a110f43d26444dedb7728b040af5
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Créer son premier conteneur dans Azure Container Instances
Azure Container Instances facilite la création et la gestion de conteneurs Docker dans Azure, sans avoir à approvisionner les machines virtuelles ou à adopter un service de niveau supérieur. Dans ce guide de démarrage rapide, vous créez un conteneur dans Azure et l’exposez sur internet avec une adresse IP publique. Cette opération s’effectue en une seule commande. En l’espace de quelques secondes, ceci s’affiche dans votre navigateur :

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][aci-app-browser]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][azure-account] avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Vous pouvez utiliser le service Azure Cloud Shell ou une installation locale de l’interface Azure CLI pour procéder à ce démarrage rapide. Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite que vous exécutiez la version 2.0.21 minimum d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les instances Azure Container Instances sont des ressources Azure et doivent être placées dans un groupe de ressources Azure, un ensemble logique dans lequel des ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create][az-group-create].

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Créez un conteneur.

Vous pouvez créer un conteneur en attribuant un nom, une image Docker ainsi qu’un groupe de ressources Azure à la commande [az container create][az-container-create]. Si vous le souhaitez, vous pouvez exposer le conteneur sur internet avec une adresse IP publique. Dans ce démarrage rapide, vous déployez un conteneur qui héberge une petite application web écrite sur la plateforme [Node.js][node-js].

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --ip-address public --ports 80
```

Après quelques secondes, vous obtenez une réponse à votre requête. Initialement, le conteneur est défini sur l’état **En cours de création**, mais il doit démarrer après quelques secondes. Vous pouvez vérifier le statut à l’aide de la commande [az container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

En bas de la sortie, vous verrez le statut de la configuration et l’adresse IP du conteneur :

```json
...
"ipAddress": {
      "ip": "13.88.176.27",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

Une fois que le statut du conteneur passe à **Réussi**, vous pouvez l’atteindre dans le navigateur en utilisant l’adresse IP obtenue.

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][aci-app-browser]

## <a name="pull-the-container-logs"></a>Extraire les journaux de conteneur

Vous pouvez extraire les journaux du conteneur que vous avez créé à l’aide de la commande [az container logs][az-container-logs] :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Sortie :

```bash
listening on port 80
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://52.224.178.107/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
```

## <a name="delete-the-container"></a>Supprimer un conteneur

Lorsque vous avez fini d’utiliser le conteneur, vous pouvez le supprimer à l’aide de la commande [az container delete][az-container-delete] :

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Pour vérifier que le conteneur a été supprimé, exécutez la commande [az container list](/cli/azure/container#az_container_list) :

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Le conteneur **mycontainer** ne doit pas apparaître dans la sortie de la commande. Si vous ne disposez d’aucun autre conteneur dans le groupe de ressources, aucune sortie ne s’affiche.

## <a name="next-steps"></a>étapes suivantes

Les codes pour le conteneur et le fichier Dockerfile utilisés dans ce guide de démarrage rapide sont disponibles [sur GitHub][app-github-repo]. Si vous voulez essayer de le créer et de le déployer dans Azure Container Instances à l’aide d’Azure Container Registry, veuillez vous référer au didacticiel sur Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiels Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Pour tester les options d’exécution des conteneurs dans un système d’orchestration sur Azure, consultez les démarrages rapides [Service Fabric][service-fabric] ou [Azure Container Service (ACS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
