---
title: "Déployer des groupes de plusieurs conteneurs dans Azure Container Instances"
description: "Découvrez comment déployer un groupe de conteneurs avec plusieurs conteneurs dans Azure Container Instances."
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5e1f23e20b001404d3f781e7e6deac87ede12684
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-a-container-group"></a>Déployer un groupe de conteneurs

Azure Container Instances prend en charge le déploiement de plusieurs conteneurs sur un seul hôte à l’aide d’un *groupe de conteneurs*. Cela est utile lors de la création d’une annexe d’application pour la journalisation, la surveillance ou toute autre configuration dans laquelle un service a besoin d’un deuxième processus associé.

Ce document décrit pas à pas une configuration simple d’annexe à plusieurs conteneurs à l’aide d’un modèle Azure Resource Manager.

## <a name="configure-the-template"></a>Configurer le modèle

Créez un fichier nommé `azuredeploy.json`, puis copiez-y le json suivant.

Dans cet exemple, un groupe de conteneurs comprenant deux conteneurs et une adresse IP publique est défini. Le premier conteneur du groupe exécute une application accessible sur Internet. Le deuxième conteneur, l’annexe, adresse une requête HTTP à l’application web principale via le réseau local du groupe.

Cet exemple d’annexe peut être étendu pour déclencher une alerte suite à la réception d’un code de réponse HTTP autre que 200 OK.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
        "location": "[resourceGroup().location]",
        "properties": {
          "containers": [
            {
              "name": "[variables('container1name')]",
              "properties": {
                "image": "[variables('container1image')]",
                "resources": {
                  "requests": {
                    "cpu": 1,
                    "memoryInGb": 1.5
                    }
                },
                "ports": [
                  {
                    "port": 80
                  }
                ]
              }
            },
            {
              "name": "[variables('container2name')]",
              "properties": {
                "image": "[variables('container2image')]",
                "resources": {
                  "requests": {
                    "cpu": 1,
                    "memoryInGb": 1.5
                    }
                }
              }
            }
          ],
          "osType": "Linux",
          "ipAddress": {
            "type": "Public",
            "ports": [
              {
                "protocol": "tcp",
                "port": "80"
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

Pour utiliser un registre d’image conteneur privé, ajoutez au document json un objet du format suivant.

```json
"imageRegistryCredentials": [
    {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
    }
]
```

## <a name="deploy-the-template"></a>Déployer le modèle

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create).

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Déployez ensuite le modèle avec la commande [az group deployment create](/cli/azure/group/deployment#create).

```azurecli-interactive
az group deployment create --name myContainerGroup --resource-group myResourceGroup --template-file azuredeploy.json
```

Après quelques secondes, vous recevez une réponse initiale d’Azure.

## <a name="view-deployment-state"></a>Afficher l’état du déploiement

Pour afficher l’état du déploiement, utilisez la commande `az container show`. Celle-ci retourne l’adresse IP publique approvisionnée via laquelle l’application est accessible.

```azurecli-interactive
az container show --name myContainerGroup --resource-group myResourceGroup -o table
```

Sortie :

```azurecli
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGrou2  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>Consulter les journaux

Consultez la sortie du journal d’un conteneur à l’aide de la commande `az container logs`. L’argument `--container-name` spécifie le conteneur à partir duquel extraire les journaux. Dans cet exemple, le premier conteneur est spécifié.

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-app --resource-group myResourceGroup
```

Sortie :

```bash
istening on port 80
::1 - - [27/Jul/2017:17:35:29 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:32 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:35 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:38 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Pour afficher les journaux du conteneur annexe, exécutez la même commande, en spécifiant le nom du deuxième conteneur.

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-sidecar --resource-group myResourceGroup
```

Sortie :

```bash
Every 3.0s: curl -I http://localhost                                                                                                                       Mon Jul 17 11:27:36 2017

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 1663
Content-Type: text/html; charset=utf-8
Last-Modified: Sun, 16 Jul 2017 02:08:22 GMT
Date: Mon, 17 Jul 2017 18:27:36 GMT
```

Comme vous pouvez le voir, l’annexe envoie régulièrement une requête HTTP à l’application web principale via le réseau local du groupe pour s’assurer qu’il fonctionne.

## <a name="next-steps"></a>Étapes suivantes

Ce document a couvert les étapes nécessaires pour le déploiement d’une instance de conteneur Azure à plusieurs conteneurs. Pour une expérience d’Azure Container Instances de bout en bout, voir le didacticiel d’Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances]: ./container-instances-tutorial-prepare-app.md
