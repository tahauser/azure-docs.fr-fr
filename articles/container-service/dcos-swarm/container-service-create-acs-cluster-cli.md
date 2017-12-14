---
title: "Déployer un cluster de conteneur Docker avec Azure CLI"
description: "Déployer une solution Kubernetes, DC/OS ou Docker Swarm dans Azure Container Service à l’aide d’Azure CLI 2.0"
services: container-service
author: sauryadas
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 40d5ea0e7abce165659219db8842ab64ac75fda7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Déployer une solution d’hébergement Conteneur Docker à l’aide d’Azure CLI 2.0

Utilisez les commandes `az acs` dans Azure CLI 2.0 pour créer et gérer des clusters dans Azure Container Service. Vous pouvez également déployer un cluster Azure Container Service à l’aide du [portail Azure](container-service-deployment.md) ou des API Azure Container Service.

Pour obtenir de l’aide sur les commandes `az acs`, transmettez le paramètre `-h` dans une commande. Par exemple : `az acs create -h`.



## <a name="prerequisites"></a>Composants requis
Pour créer un cluster Azure Container Service à l’aide d’Azure CLI 2.0, vous devez :
* disposer d’un compte Azure ([obtenir une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/))
* avoir installé et configuré [Azure CLI 2.0](/cli/azure/install-az-cli2)

## <a name="get-started"></a>Prise en main 
### <a name="log-in-to-your-account"></a>Se connecter à son compte
```azurecli
az login 
```

Suivez les invites pour vous connecter de manière interactive. Pour d’autres méthodes de connexion, consultez [Prise en main d’Azure CLI 2.0](/cli/azure/get-started-with-az-cli2).

### <a name="set-your-azure-subscription"></a>Définir votre abonnement Azure

Si vous disposez de plusieurs abonnements Azure, définissez l’abonnement par défaut. Par exemple :

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Nous vous recommandons de créer un groupe de ressources pour chaque cluster. Spécifiez une région Azure dans laquelle Azure Container Service est [disponible](https://azure.microsoft.com/en-us/regions/services/). Par exemple :

```azurecli
az group create -n acsrg1 -l "westus"
```
Le résultat ressemble à ce qui suit :

![Créer un groupe de ressources](./media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Créer un cluster Azure Container Service

Pour créer un cluster, utilisez `az acs create`.
Un nom pour le cluster et le nom du groupe de ressources créé à l’étape précédente sont des paramètres obligatoires. 

Les autres paramètres sont définis sur les valeurs par défaut (voir l’écran suivant), sauf s’ils sont remplacés via leurs commutateurs respectifs. Par exemple, l’Orchestrator est défini par défaut sur DC/OS. Et si vous n’en spécifiez pas un, un préfixe de nom DNS est créé sur la base du nom du cluster.

![création d’un cluster acs az](./media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>`acs create` rapide à l’aide des valeurs par défaut
Si vous disposez d’un fichier de clé publique SSH RSA `id_rsa.pub` dans l’emplacement par défaut (ou que vous en avez créé un pour [OS X et Linux](../../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../../virtual-machines/linux/ssh-from-windows.md)), utilisez une commande semblable à ce qui suit :

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
Si vous n’avez pas de clé publique SSH, utilisez cette seconde commande. Cette commande avec le commutateur `--generate-ssh-keys` en crée une pour vous.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

Après avoir saisi la commande, attendez environ 10 minutes que le cluster soit créé. La sortie de la commande inclut des noms de domaine complet (FQDN) de nœuds principal et d’agent et une commande SSH pour se connecter au premier principal. Voici la sortie abrégée :

![Image création du cluster ACS](./media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> La [procédure pas à pas Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md) montre comment utiliser `az acs create` avec les valeurs par défaut pour créer un cluster Kubernetes.
>

## <a name="manage-acs-clusters"></a>Gérer des clusters ACS

Utilisez des commandes `az acs` supplémentaires pour gérer votre cluster. Voici quelques exemples.

### <a name="list-clusters-under-a-subscription"></a>Répertorier les clusters dans un abonnement

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>Répertorier les clusters au sein d’un groupe de ressources

```azurecli
az acs list -g acsrg1 --output table
```

![liste des clusters acs](./media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>Afficher les détails d’un cluster Container Service

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![affichage du cluster acs](./media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>Mettre à l’échelle le cluster
Vous pouvez effectuer une mise à l’échelle verticale ou horizontale des nœuds d’agent. Le paramètre `new-agent-count` indique le nouveau nombre d’agents dans le cluster ACS.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![mise à l’échelle du cluster acs](./media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Supprimer un cluster Container Service
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
Cette commande ne supprime pas toutes les ressources (réseau et stockage) créées lors de la création du service de conteneur. Pour supprimer toutes les ressources facilement, il est recommandé de déployer chaque cluster dans un groupe de ressources distinct. Supprimez ensuite le groupe de ressources lorsque le cluster n’est plus nécessaire.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous disposez d’un cluster opérationnel, consultez les documents suivants pour obtenir des informations supplémentaires concernant la connexion et la gestion du cluster :

* [Connexion à un cluster Azure Container Service](../container-service-connect.md)
* [Gestion de conteneur via l’API REST](container-service-mesos-marathon-rest.md)
* [Gestion des conteneurs avec Docker Swarm](container-service-docker-swarm.md)
* [Gestion des conteneurs avec Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md)