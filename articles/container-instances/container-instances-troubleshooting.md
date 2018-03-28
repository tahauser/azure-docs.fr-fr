---
title: Résolution des problèmes liés à Azure Container Instances
description: Découvrez comment résoudre les problèmes liés à Azure Container Instances
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a527939d6bc73e3dee5701bc53ef8312e68d2953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Résoudre les problèmes de déploiement liés à Azure Container Instances

Cet article explique comment résoudre les problèmes de déploiement de conteneurs sur Azure Container Instances. Il décrit également les problèmes courants que vous pouvez rencontrer.

## <a name="view-logs-and-stream-output"></a>Afficher les journaux et sortie de flux

Lorsque vous avez un conteneur mal configuré, démarrez en affichant ses journaux avec [journaux du conteneur az][az-container-logs] et diffusez en continu sa sortie standard et son erreur standard avec [liaison du conteneur az] [az-container-attach].

### <a name="view-logs"></a>Consulter les journaux

Pour afficher les journaux à partir de votre code d’application dans un conteneur, vous pouvez utiliser la commande [az container logs][az-container-logs].

Voici la sortie du journal à partir de l’exemple de conteneur basé sur des tâches dans [Exécuter une tâche en conteneur dans ACI](container-instances-restart-policy.md), après avoir chargé une URL non valide à traiter :

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

### <a name="attach-output-streams"></a>Joindre des flux de sortie

La commande [liaison de conteneur az][az-container-attach] fournit des informations de diagnostic pendant le démarrage du conteneur. Une fois le conteneur démarré, il diffuse en continu STDOUT et STDERR sur votre console locale.

Par exemple, voici la sortie du conteneur basé sur des tâches dans [Exécuter une tâche en conteneur dans ACI](container-instances-restart-policy.md), après avoir fourni une URL valide d’un fichier texte volumineux à traiter :

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Récupérer des événements de diagnostic

Toutefois, si votre conteneur ne se déploie pas correctement, vous devez examiner les informations de diagnostic fournies par le fournisseur de ressources Azure Container Instances. Pour afficher les événements liés à votre conteneur, exécutez la commande [az container show][az-container-show] :

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

Les sections suivantes décrivent les problèmes courants de ce compte pour la plupart des erreurs de déploiement de conteneur :

* [Version d’image non prise en charge](#image-version-not-supported)
* [Impossible d’extraire l’image](#unable-to-pull-image)
* [Le conteneur s’arrête et redémarre en permanence](#container-continually-exits-and-restarts)
* [Le démarrage du conteneur prend beaucoup de temps](#container-takes-a-long-time-to-start)
* [Erreur « ressource non disponible »](#resource-not-available-error)

## <a name="image-version-not-supported"></a>Version d’image non prise en charge

Si l’image spécifiée n’est pas prise en charge par Azure Container Instances, une erreur `ImageVersionNotSupported` est renvoyée. La valeur de l’erreur est `The version of image '{0}' is not supported.` et s’applique généralement aux images Windows 1709. Pour résoudre ce problème, utilisez une image LTS Windows. La prise en charge pour des images Windows 1709 est en cours d’élaboration.

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

Les deux principaux facteurs qui contribuent à l’heure de démarrage dans Azure Container Instances sont :

* [Taille de l'image](#image-size)
* [Emplacement de l’image](#image-location)

Les images Windows ont des [considérations supplémentaires](#use-recent-windows-images).

### <a name="image-size"></a>Taille de l'image

Si le démarrage de votre conteneur prend beaucoup de temps, commencez par examiner la taille de votre image conteneur. Étant donné qu’Azure Container Instances extrait l’image conteneur à la demande, le temps de démarrage est directement lié à la taille de cette image.

Vous pouvez afficher la taille de l’image de votre conteneur à l’aide de la commande `docker images` dans l’interface CLI Docker :

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Pour que l’image conserve une petite taille, faites en sorte que l’image finale ne contienne aucun élément qui soit superflu au moment de l’exécution. Pour ce faire, vous pouvez utiliser des [builds à plusieurs étapes][docker-multi-stage-builds]. Grâce aux builds à plusieurs étapes, vous pouvez facilement faire en sorte que l’image finale ne contienne que les artefacts nécessaires à votre application, à l’exclusion de tout contenu supplémentaire qui était requis au moment de la génération.

### <a name="image-location"></a>Emplacement de l’image

Il existe une autre façon de réduire l’impact de l’extraction de l’image sur le temps de démarrage de votre conteneur. Elle consiste à héberger l’image du conteneur dans [Azure Container Registry](/azure/container-registry/) dans la région où vous envisagez de déployer Azure Container Instances. Cette opération raccourcit le chemin réseau que l’image conteneur doit parcourir, réduisant considérablement le temps de téléchargement.

### <a name="use-recent-windows-images"></a>Utiliser des images récentes Windows

Azure Container Instances utilise un mécanisme de mise en cache pour aider à accélérer le démarrage du conteneur pour des images basées sur certaines images Windows.

Pour garantir le meilleur temps de démarrage du conteneur Windows, utilisez une des **trois dernières** versions des **deux images** suivantes en tant qu’image de base :

* [Windows Server 2016][docker-hub-windows-core] (LTS uniquement)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

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
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show