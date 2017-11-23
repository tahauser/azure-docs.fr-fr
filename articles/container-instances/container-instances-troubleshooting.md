---
title: "Résolution des problèmes liés à Azure Container Instances"
description: "Découvrez comment résoudre les problèmes liés à Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 78bd45f7f71fd25e351d4e9b922a6a3f171437fd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Résoudre les problèmes de déploiement liés à Azure Container Instances

Cet article explique comment résoudre les problèmes de déploiement de conteneurs sur Azure Container Instances. Il décrit également les problèmes courants que vous pouvez rencontrer.

## <a name="get-diagnostic-events"></a>Récupérer des événements de diagnostic

Pour afficher les journaux à partir de votre code d’application dans un conteneur, vous pouvez utiliser la commande [az container logs](/cli/azure/container#logs). Toutefois, si votre conteneur ne se déploie pas correctement, vous devez examiner les informations de diagnostic fournies par le fournisseur de ressources Azure Container Instances. Pour afficher les événements liés à votre conteneur, exécutez la commande suivante :

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

La sortie inclut les propriétés principales de votre conteneur, ainsi que les événements de déploiement :

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>Problèmes de déploiement courants

La plupart des erreurs de déploiement sont liées à quelques problèmes courants.

## <a name="unable-to-pull-image"></a>Impossible d’extraire l’image

Si Azure Container Instances ne parvient pas à extraire votre image initialement, il réessaie pendant une certaine période, sans succès. Des événements tels que les suivants sont alors affichés :

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

Pour résoudre cette situation, supprimez le conteneur et essayez de le redéployer, en veillant à taper correctement le nom de l’image.

## <a name="container-continually-exits-and-restarts"></a>Le conteneur s’arrête et redémarre en permanence

Si votre conteneur s’exécute jusqu’à complétion et redémarre automatiquement, vous devrez peut-être définir une [stratégie de redémarrage](container-instances-restart-policy.md) avec la valeur **OnFailure** ou **Never**. Si vous spécifiez **OnFailure** et constatez encore des redémarrages continus, il peut y avoir un problème avec l’application ou le script exécutés dans votre conteneur.

L’API Container Instances inclut une propriété `restartCount`. Pour vérifier le nombre de redémarrages d’un conteneur, vous pouvez utiliser la commande [az container show](/cli/azure/container#az_container_show) dans Azure CLI 2.0. Dans l’exemple de sortie suivant (qui a été tronqué par souci de concision), vous pouvez voir la propriété `restartCount` à la fin de la sortie.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> La plupart des images conteneur pour les distributions Linux définissent un interpréteur de commandes, tel que bash, comme commande par défaut. Un interpréteur de commandes n’étant pas en soi un service de longue durée, ces conteneurs quittent la procédure immédiatement et entrent dans une boucle de redémarrage lorsqu’ils sont configurés avec la stratégie de redémarrage par défaut (**Always**).

## <a name="container-takes-a-long-time-to-start"></a>Le démarrage du conteneur prend beaucoup de temps

Si le démarrage de votre conteneur prend beaucoup de temps, commencez par examiner la taille de votre image conteneur. Étant donné qu’Azure Container Instances extrait l’image conteneur à la demande, le temps de démarrage est directement lié à la taille de cette image.

Vous pouvez afficher la taille de votre image conteneur à l’aide de l’interface CLI Docker :

```bash
docker images
```

Output:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

Pour que l’image conserve une petite taille, faites en sorte que l’image finale ne contienne aucun élément qui soit superflu au moment de l’exécution. Pour ce faire, vous pouvez utiliser des [builds à plusieurs étapes](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). Grâce aux builds à plusieurs étapes, vous pouvez facilement faire en sorte que l’image finale ne contienne que les artefacts nécessaires à votre application, à l’exclusion de tout contenu supplémentaire qui était requis au moment de la génération.

Une autre façon de réduire l’impact de l’extraction de l’image sur le temps de démarrage de votre conteneur consiste à héberger l’image conteneur à l’aide d’Azure Container Registry dans la région où vous envisagez d’utiliser Azure Container Instances. Cette opération raccourcit le chemin réseau que l’image conteneur doit parcourir, réduisant considérablement le temps de téléchargement.

## <a name="resource-not-available-error"></a>Erreur : ressource non disponible

En raison d’une charge de ressources régionales variable dans Azure, vous pouvez recevoir l’erreur suivante quand vous tentez de déployer une instance de conteneur :

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Cette erreur indique qu’en raison d’une charge importante dans la région dans laquelle vous tentez de déployer, les ressources spécifiées pour votre conteneur ne peuvent pas être allouées à ce moment-là. Utilisez une ou plusieurs des étapes d’atténuation suivantes pour vous aider à résoudre votre problème.

* Vérifier que les paramètres de votre déploiement de conteneur correspondent à ceux définis dans [Disponibilité des régions pour Azure Container Instances](container-instances-region-availability.md)
* Spécifier des paramètres de processeur et de mémoire inférieurs pour le conteneur
* Déployer sur une autre région Azure
* Déployer plus tard
