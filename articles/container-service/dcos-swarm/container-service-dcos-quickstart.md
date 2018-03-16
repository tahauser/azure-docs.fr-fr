---
title: "Démarrage rapide avec Azure Container Service - Déployer un cluster DC/OS"
description: "Démarrage rapide avec Azure Container Service - Déployer un cluster DC/OS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2a0c702f1b1997a35be49c8d94db9ff69e95653f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="deploy-a-dcos-cluster"></a>Déployer un cluster DC/OS

DC/OS propose une plateforme distribuée destinée à exécuter des applications en conteneur modernes. Avec Azure Container Service, l’approvisionnement d’un cluster DC/OS prêt pour la production est une opération simple et rapide. Ce démarrage rapide décrit en détail les étapes de base à suivre pour déployer un cluster DC/OS et exécuter une charge de travail de base.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce didacticiel requiert Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/reference-index#az_login) et suivez les instructions à l’écran.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Créer un cluster DC/OS

Pour créer un cluster DC/OS, utilisez la commande [az acs create](/cli/azure/acs#az_acs_create).

L’exemple suivant crée un cluster DC/OS nommé *myDCOSCluster* et crée des clés SSH si elles n’existent pas déjà. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

Dans certains cas, par exemple avec une version d’évaluation limitée, un abonnement Azure dispose d’un accès limité aux ressources Azure. Si le déploiement échoue à cause d’une limitation du nombre de cœurs disponibles, réduisez le nombre d’agents par défaut en ajoutant `--agent-count 1` à la commande [az acs create](/cli/azure/acs#az_acs_create). 

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

Le tunnel SSH peut être testé en accédant à `http://localhost`. Si le port utilisé n’est pas le port 80, modifiez l’emplacement en conséquence. 

Si le tunnel SSH a bien été créé, le portail DC/OS est retourné.

![Interface utilisateur DC/OS](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Installer l’interface de ligne de commande DC/OS

L’interface de ligne de commande (CLI) DC/OS permet de gérer un cluster DC/OS à partir de la ligne de commande. Installez l’interface CLI DC/OS en utilisant la commande [az acs dcos install-cli](/azure/acs/dcos#install-cli). Si vous utilisez Azure CloudShell, l’interface CLI DC/OS est déjà installée. 

Si vous exécutez Azure CLI sur macOS ou Linux, vous devrez peut-être exécuter la commande avec sudo.

```azurecli
az acs dcos install-cli
```

Avant de pouvoir être utilisée avec le cluster, l’interface CLI doit être configurée pour utiliser le tunnel SSH. Pour ce faire, exécutez la commande suivante en modifiant le port, si nécessaire.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Exécuter une application

Le mécanisme de planification par défaut d’un cluster ACS DC/OS est Marathon. Marathon permet de démarrer une application et de gérer son état sur le cluster DC/OS. Pour planifier une application via Marathon, créez un fichier sous le nom *marathon-app.json* et copiez-y le contenu suivant. 

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
dcos marathon app add marathon-app.json
```

Pour afficher l’état de déploiement de l’application, exécutez la commande suivante.

```azurecli
dcos marathon app list
```

Quand la valeur de la colonne **WAITING** passe de *True* à *False*, le déploiement de l’application est terminé.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Procurez-vous l’adresse IP publique des agents du cluster DC/OS.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Le site NGINX par défaut vous est retourné dès lors que vous accédez à cette adresse.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Supprimer un cluster DC/OS

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le cluster DC/OS et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez déployé un cluster DC/OS et exécuté un conteneur Docker simple sur le cluster. Pour en savoir plus sur Azure Container Service, passez aux didacticiels ACS.

> [!div class="nextstepaction"]
> [Gérer un cluster ACS DC/OS](container-service-dcos-manage-tutorial.md)