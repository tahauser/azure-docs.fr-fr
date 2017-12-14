---
title: "Gérer le cluster DC/OS Azure avec l’API REST Marathon"
description: "Déployez des conteneurs vers un cluster DC/OS Azure Container Service à l’aide de l’API REST Marathon."
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: c9322756c30011305ebe6f4f2fd38554f275a1b3
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Gestion de conteneur DC/OS au moyen de l’API REST Marathon

DC/OS offre un environnement de déploiement et de mise à l’échelle des charges de travail en cluster tout en faisant abstraction du matériel sous-jacent. DC/OS sous-tend une infrastructure qui gère la planification et l’exécution des charges de travail de calcul. Bien qu’il existe des infrastructures pour de nombreuses charges de travail courantes, ce document décrit la création et la mise à l’échelle des déploiements de conteneurs avec l’API REST Marathon. 

## <a name="prerequisites"></a>Composants requis

Avant d’étudier ces exemples, vous devez avoir un cluster DC/OS configuré dans Azure Container Service. Vous devez également disposer d’une connectivité à distance à ce cluster. Pour plus d’informations sur ces éléments, voir les articles suivants :

* [Déploiement d’un cluster Azure Container Service](container-service-deployment.md)
* [Connexion à un cluster Azure Container Service](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Accéder aux API DC/OS
Une fois que vous êtes connecté au cluster Azure Container Service, vous pouvez accéder à DC/OS et aux API REST associées via http://localhost:local-port. Les exemples cités dans ce document partent du principe que vous créez un tunnel sur le port 80. Par exemple, les points de terminaison Marathon peuvent être atteints via les URI commençant par `http://localhost/marathon/v2/`. 

Pour plus d’informations sur les différentes API, consultez la documentation Mesosphere relative à l’[API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) et à l’[API Chronos](https://mesos.github.io/chronos/docs/api.html), ainsi que la documentation Apache relative à l’[API Mesos Scheduler](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Collecte d’informations à partir de DC/OS et de Marathon
Avant de déployer des conteneurs vers le cluster DC/OS, vous devez recueillir certaines informations sur le cluster DC/OS, notamment le nom et l’état des agents DC/OS. Pour ce faire, interrogez le point de terminaison `master/slaves` sur l’API REST DC/OS. Si tout se déroule correctement, la requête renvoie une liste d’agents DC/OS accompagnée de quelques-unes de leurs propriétés.

```bash
curl http://localhost/mesos/master/slaves
```

À présent, utilisez le point de terminaison `/apps` de Marathon pour vérifier les déploiements d’application actuels vers le cluster DC/OS. S’il s’agit d’un nouveau cluster, un tableau vide s’affiche pour les applications.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Déployer un conteneur au format Docker
Vous déployez les conteneurs au format Docker via l’API REST Marathon à l’aide d’un fichier JSON décrivant le déploiement souhaité. L’exemple suivant déploie un conteneur Nginx vers un agent privé dans le cluster. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Pour déployer un conteneur au format Docker, stockez le fichier JSON dans un emplacement accessible. Ensuite, exécutez la commande suivante pour déployer le conteneur. Spécifiez le nom du fichier JSON (`marathon.json` dans cet exemple).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Le résultat ressemble à ce qui suit :

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

À présent, si vous interrogez Marathon à propos des applications, cette nouvelle application apparaît dans la sortie.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Atteindre le conteneur

Vous pouvez vérifier que Nginx s’exécute dans un conteneur sur un des agents privés du cluster. Pour trouver l’hôte et le port sur lesquels le conteneur s’exécute, demandez à Marathon les tâches en cours d’exécution : 

```bash
curl localhost/marathon/v2/tasks
```

Recherchez la valeur de `host` dans la sortie (une adresse IP similaire à `10.32.0.x`) et la valeur de `ports`.


À présent, établissez une connexion au terminal SSH (pas une connexion par tunnel) au nom de domaine complet de gestion du cluster. Une fois la connexion établie, effectuez la demande suivante, en remplaçant les valeurs correctes de `host` et `ports` :

```bash
curl http://host:ports
```

Le résultat du serveur Nginx ressemble à ce qui suit :

![Nginx à partir d’un conteneur](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Mettre vos conteneurs à l’échelle
Vous pouvez utiliser l’API Marathon pour diminuer ou augmenter la taille des déploiements des instances d’application. Dans l’exemple précédent, vous avez déployé une instance d’une application. Nous allons augmenter la taille de déploiement pour obtenir trois instances d’une application. Pour ce faire, créez un fichier JSON avec le texte JSON suivant et stockez-le dans un emplacement accessible.

```json
{ "instances": 3 }
```

À partir de la connexion par tunnel, exécutez la commande suivante pour augmenter la taille des instances de l’application.

> [!NOTE]
> L’URI est http://localhost/marathon/v2/apps/, suivi de l’ID de l’application que vous souhaitez mettre à l’échelle. Si vous utilisiez l’exemple Nginx fourni ici, l’URI serait http://localhost/marathon/v2/apps/nginx.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Pour finir, interrogez le point de terminaison Marathon sur les applications. Vous constatez qu’il existe désormais trois conteneurs Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Commandes PowerShell équivalentes
Vous pouvez effectuer les mêmes opérations sur un système Windows à l’aide des commandes PowerShell.

Pour collecter des informations sur le cluster DC/OS (par exemple, le nom et l’état de l’agent), exécutez la commande suivante :

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Vous déployez les conteneurs au format Docker via Marathon à l’aide d’un fichier JSON décrivant le déploiement souhaité. L’exemple ci-après déploie le conteneur Nginx en liant le port 80 de l’agent DC/OS au port 80 du conteneur.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Pour déployer un conteneur au format Docker, stockez le fichier JSON dans un emplacement accessible. Ensuite, exécutez la commande suivante pour déployer le conteneur. Spécifiez le chemin d’accès au fichier JSON (`marathon.json` dans cet exemple).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Vous pouvez également utiliser l’API Marathon pour diminuer ou augmenter la taille des déploiements des instances d’application. Dans l’exemple précédent, vous avez déployé une instance d’une application. Nous allons augmenter la taille de déploiement pour obtenir trois instances d’une application. Pour ce faire, créez un fichier JSON avec le texte JSON suivant et stockez-le dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante pour augmenter la taille des instances de l’application :

> [!NOTE]
> L’URI est http://localhost/marathon/v2/apps/, suivi de l’ID de l’application que vous souhaitez mettre à l’échelle. Si vous utilisiez l’exemple Nginx fourni ici, l’URI serait http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les points de terminaison HTTP Mesos](http://mesos.apache.org/documentation/latest/endpoints/)
* [En savoir plus sur l’API REST Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)

