---
title: "Déployer des groupes de plusieurs conteneurs dans Azure Container Instances"
description: "Découvrez comment déployer un groupe de conteneurs avec plusieurs conteneurs dans Azure Container Instances."
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/10/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 41a47adb1f1da417038757934f0a6cf7e11555da
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="deploy-a-container-group"></a>Déployer un groupe de conteneurs

Azure Container Instances prend en charge le déploiement de plusieurs conteneurs sur un seul hôte à l’aide d’un [groupe de conteneurs](container-instances-container-groups.md). Cela est utile lors de la création d’une annexe d’application pour la journalisation, la surveillance ou toute autre configuration dans laquelle un service a besoin d’un deuxième processus associé.

Ce document décrit pas à pas l’exécution d’une configuration simple d’annexe à plusieurs conteneurs à l’aide d’un modèle Azure Resource Manager.

> [!NOTE]
> Les groupes à plusieurs conteneurs sont actuellement restreints aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences de la plateforme actuelle dans [Quotas et disponibilité des régions pour Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-template"></a>Configurer le modèle

Créez un fichier nommé `azuredeploy.json`, puis copiez dans celui-ci le code JSON suivant.

Dans cet exemple, un groupe de conteneurs est défini. Il comprend deux conteneurs, une adresse IP publique et deux ports exposés. Le premier conteneur du groupe exécute une application accessible sur Internet. Le deuxième conteneur, l’annexe, adresse une requête HTTP à l’application web principale via le réseau local du groupe.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
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
      "apiVersion": "2017-10-01-preview",
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
                },
                {
                  "port": 8080
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
            },
            {
                "protocol": "tcp",
                "port": "8080"
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

Pour utiliser un registre d’image de conteneur privé, ajoutez au document JSON un objet du format suivant.

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

Créez un groupe de ressources avec la commande [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Déployez ensuite le modèle avec la commande [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --name myContainerGroup --template-file azuredeploy.json
```

Après quelques secondes, vous devriez recevoir une réponse initiale d’Azure.

## <a name="view-deployment-state"></a>Afficher l’état du déploiement

Pour afficher l’état du déploiement, utilisez la commande [az container show][az-container-show]. Celle-ci renvoie l’adresse IP publique mise en service via laquelle l’application est accessible.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Sortie :

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Consulter les journaux

Consultez la sortie du journal d’un conteneur à l’aide de la commande [az container logs][az-container-logs]. L’argument `--container-name` spécifie le conteneur à partir duquel extraire les journaux. Dans cet exemple, le premier conteneur est spécifié.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Sortie :

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Pour afficher les journaux du conteneur annexe, exécutez la même commande, en spécifiant le nom du deuxième conteneur.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Sortie :

```bash
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Comme vous pouvez le voir, l’annexe envoie régulièrement une requête HTTP à l’application web principale via le réseau local du groupe pour s’assurer qu’il fonctionne. Cet exemple d’annexe peut être étendu pour déclencher une alerte suite à la réception d’un code de réponse HTTP autre que 200 OK.

## <a name="next-steps"></a>Étapes suivantes

Ce document a couvert les étapes nécessaires pour le déploiement d’une instance Azure Container à plusieurs conteneurs. Pour une expérience d’Azure Container Instances de bout en bout, voir le didacticiel d’Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
