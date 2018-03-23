---
title: Utiliser OpenFaaS avec Azure Container Service (AKS)
description: Déployer et utiliser OpenFaaS avec Azure Container Service (AKS)
services: container-service
author: justindavies
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 06706450d8af6f571f002789815290f75da9623d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="using-openfaas-on-aks"></a>Utilisation d’OpenFaaS avec AKS

[OpenFaaS][open-faas] est un framework qui permet de créer des fonctions serverless sur des conteneurs. Ce projet open source a été très largement adopté par la communauté. Ce document décrit en détail l’installation et l’utilisation d’OpenFaas sur un cluster Azure Container Service (AKS).

## <a name="prerequisites"></a>Prérequis


Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

* Des connaissances de base sur Kubernetes
* Un cluster Azure Container Service (AKS) et des informations d’identification AKS configurées sur votre système de développement
* Azure CLI installé sur votre système de développement.
* L’installation des outils en ligne de commande Git sur votre système

## <a name="get-openfaas"></a>Obtenir OpenFaaS

Clonez le dépôt du projet OpenFaaS sur votre système de développement.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Passez au répertoire du dépôt cloné.

```azurecli-interactive
cd faas-netes 
```

## <a name="deploy-openfaas"></a>Déployer OpenFaaS

Il est recommandé de stocker OpenFaaS et ses fonctions dans leur propre espace de noms Kubernetes.

Créez un espace de noms pour le système OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Créez un deuxième espace de noms pour les fonctions OpenFaaS.

```azurecli-interactive 
kubectl create namespace openfaas-fn
```

Un graphique Helm pour OpenFaaS est inclus dans le dépôt cloné. Utilisez ce graphique pour déployer OpenFaaS dans votre cluster AKS.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/ 
```

Sortie :

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Une adresse IP publique est créée pour accéder à la passerelle OpenFaaS. Pour récupérer l’adresse IP publique, utilisez la commande [kubectl get service][kubectl-get]. L’affectation de l’adresse IP au service peut prendre un certain temps.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Sortie : 

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Pour tester le système OpenFaaS, accédez à l’adresse IP externe sur le port 8080 (`http://52.186.64.52:8080` dans cet exemple).

![Interface utilisateur d’OpenFaaS](media/container-service-serverless/openfaas.png)

Enfin, installez l’interface CLI d’OpenFaaS. Cet exemple utilise brew. Pour d’autres options, consultez la [documentation relative à l’interface CLI d’OpenFaaS][open-faas-cli].

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Créer la première fonction

Maintenant qu’OpenFaaS est opérationnel, créez une fonction à l’aide du portail OpenFaas.

Cliquez sur **Deploy New Function** (Déployer une nouvelle fonction), puis recherchez **Figlet**. Sélectionnez la fonction Figlet, puis cliquez sur **Deploy** (Déployer).

![Figlet](media/container-service-serverless/figlet.png)

Utilisez curl pour appeler la fonction. Dans l’exemple suivant, remplacez l’adresse IP par celle de votre passerelle OpenFaas.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Output:

```console
 _   _      _ _            _                        
| | | | ___| | | ___      / \    _____   _ _ __ ___ 
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Créer la deuxième fonction

À présent, créez la deuxième fonction. Cet exemple est déployé à l’aide de l’interface CLI d’OpenFaaS. Il inclut une image de conteneur personnalisée et récupère des données à partir de Cosmos DB. Plusieurs éléments doivent être configurés avant la création de la fonction. 

D’abord, créez un groupe de ressources pour Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Déployez une instance CosmosDB de type `MongoDB`. L’instance doit avoir un nom unique. Vous devez donc remplacer `openfaas-cosmos` par un nom unique dans votre environnement. 

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Récupérez la chaîne de connexion de base de données Cosmos et stockez-la dans une variable. 

Remplacez la valeur de l’argument `--resource-group` par le nom de votre groupe de ressources, et la valeur de l’argument `--name` par le nom de votre instance Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Maintenant, remplissez l’instance Cosmos DB avec des données de test. Créez un fichier nommé `plans.json` et copiez-y le code json suivant.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Utilisez l’outil *mongoimport* pour charger des données dans l’instance CosmosDB. 

Si nécessaire, installez les outils MongoDB. L’exemple suivant installe ces outils à l’aide de brew. Pour d’autres options, consultez la [documentation MongoDB][install-mongo].

```azurecli-interactive
brew install mongodb
```

Chargez les données dans la base de données.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Output:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Exécutez la commande suivante pour créer la fonction : Remplacez la valeur de l’argument `-g` par l’adresse de votre passerelle OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Une fois déployée, vous devez voir le point de terminaison OpenFaaS de la fonction que vous venez de créer.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Testez la fonction à l’aide de curl. Remplacez l’adresse IP par l’adresse de la passerelle OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Output:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

Vous pouvez également tester la fonction dans l’interface utilisateur d’OpenFaaS.

![texte de remplacement](media/container-service-serverless/OpenFaaSUI.png)

# <a name="next-steps"></a>Étapes suivantes

Le déploiement par défaut d’OpenFaas doit être verrouillé pour les fonctions et pour la passerelle OpenFaaS. Ce [billet de blog d’Alex Ellis](https://blog.alexellis.io/lock-down-openfaas/) comprend plus de détails sur les options de configuration sécurisée. 

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli