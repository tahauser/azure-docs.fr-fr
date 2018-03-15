---
title: "Résolution des problèmes liés à Azure Container Instances"
description: "Découvrez comment résoudre les problèmes liés à Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 561729e5e495500222ccec5b4b536a3152cb25e3
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Résoudre les problèmes de déploiement liés à Azure Container Instances

Cet article explique comment résoudre les problèmes de déploiement de conteneurs sur Azure Container Instances. Il décrit également les problèmes courants que vous pouvez rencontrer.

## <a name="get-diagnostic-events"></a>Récupérer des événements de diagnostic

Pour afficher les journaux à partir de votre code d’application dans un conteneur, vous pouvez utiliser la commande [az container logs][az-container-logs]. Toutefois, si votre conteneur ne se déploie pas correctement, vous devez examiner les informations de diagnostic fournies par le fournisseur de ressources Azure Container Instances. Pour afficher les événements liés à votre conteneur, exécutez la commande [az container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

La sortie inclut les propriétés principales de votre conteneur, ainsi que les événements de déploiement (qui apparaissent tronqués ici) :

```JSON
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
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>Problèmes de déploiement courants

La plupart des erreurs de déploiement sont liées à quelques problèmes courants.

## <a name="image-version-not-supported"></a>Version d’image non prise en charge

Si l’image spécifiée n’est pas prise en charge par Azure Container Instances, une erreur est renvoyée sous la forme `ImageVersionNotSupported`. La valeur d’erreur affichée est `The version of image '{0}' is not supported.`. Cette erreur s’applique actuellement à des images Windows 1709. Pour l’éviter, utilisez une image LTS Windows. La prise en charge pour des images Windows 1709 est en cours d’élaboration.

## <a name="unable-to-pull-image"></a>Impossible d’extraire l’image

Si Azure Container Instances ne parvient pas à extraire votre image initialement, il réessaie pendant une certaine période, sans succès. Des événements tels que les suivants sont alors affichés dans la sortie de [az container show][az-container-show] :

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Pour résoudre cette situation, supprimez le conteneur et essayez de le redéployer, en veillant à taper correctement le nom de l’image.

## <a name="container-continually-exits-and-restarts"></a>Le conteneur s’arrête et redémarre en permanence

Si votre conteneur s’exécute jusqu’à complétion et redémarre automatiquement, vous devrez peut-être définir une [stratégie de redémarrage](container-instances-restart-policy.md) avec la valeur **OnFailure** ou **Never**. Si vous spécifiez **OnFailure** et constatez encore des redémarrages continus, il peut y avoir un problème avec l’application ou le script exécutés dans votre conteneur.

L’API Container Instances inclut une propriété `restartCount`. Pour vérifier le nombre de redémarrages d’un conteneur, vous pouvez utiliser la commande [az container show][az-container-show] dans Azure CLI 2.0. Dans l’exemple de sortie suivant (qui a été tronqué par souci de concision), vous pouvez voir la propriété `restartCount` à la fin de la sortie.

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

Pour que l’image conserve une petite taille, faites en sorte que l’image finale ne contienne aucun élément qui soit superflu au moment de l’exécution. Pour ce faire, vous pouvez utiliser des [builds à plusieurs étapes][docker-multi-stage-builds]. Grâce aux builds à plusieurs étapes, vous pouvez facilement faire en sorte que l’image finale ne contienne que les artefacts nécessaires à votre application, à l’exclusion de tout contenu supplémentaire qui était requis au moment de la génération.

Une autre façon de réduire l’impact de l’extraction de l’image sur le temps de démarrage de votre conteneur consiste à héberger l’image conteneur à l’aide d’Azure Container Registry dans la région où vous envisagez d’utiliser Azure Container Instances. Cette opération raccourcit le chemin réseau que l’image conteneur doit parcourir, réduisant considérablement le temps de téléchargement.

## <a name="resource-not-available-error"></a>Erreur : ressource non disponible

En raison d’une charge de ressources régionales variable dans Azure, vous pouvez recevoir l’erreur suivante quand vous tentez de déployer une instance de conteneur :

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Cette erreur indique qu’en raison d’une charge importante dans la région dans laquelle vous tentez de déployer, les ressources spécifiées pour votre conteneur ne peuvent pas être allouées à ce moment-là. Utilisez une ou plusieurs des étapes d’atténuation suivantes pour vous aider à résoudre votre problème.

* Vérifier que les paramètres de votre déploiement de conteneur correspondent à ceux définis dans [Quotas et disponibilité des régions pour Azure Container Instances](container-instances-quotas.md#region-availability)
* Spécifier des paramètres de processeur et de mémoire inférieurs pour le conteneur
* Déployer sur une autre région Azure
* Déployer plus tard

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/

<!-- LINKS - Internal -->
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show