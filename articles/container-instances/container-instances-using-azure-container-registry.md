---
title: "Déployer sur Azure Container Instances à partir d’Azure Container Registry"
description: "Déployer Azure Container Instances à partir d’Azure Container Registry"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 4205b47dc67920021812c1e573a98de64ad198ec
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Déployer sur Azure Container Instances à partir d’Azure Container Registry

Azure Container Registry est un registre privé Azure pour les images conteneur Docker. Cet article explique comment déployer des images conteneur stockées dans Azure Container Registry sur Azure Container Instances.

## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

L’interface CLI Azure inclut des commandes pour la création et la gestion des conteneurs dans Azure Container Instances. Si vous spécifiez une image privée dans la commande [az container create][az-container-create], vous pouvez également spécifier le mot de passe du registre d’image nécessaire pour s’authentifier auprès du registre de conteneurs.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

La commande [az container create][az-container-create] accepte également la spécification de `--registry-login-server` et de `--registry-username`. Toutefois, le serveur de connexion pour le registre Azure Container Registry est toujours *registryname*. azurecr.io et le nom d’utilisateur par défaut est *registryname*, de sorte que ces valeurs sont déduites à partir du nom de l’image si elles ne sont pas fournies de manière explicite.

## <a name="deploy-with-azure-resource-manager-template"></a>Déploiement avec un modèle Azure Resource Manager

Vous pouvez spécifier les propriétés de votre registre Azure Container Registry dans un modèle Azure Resource Manager en incluant la `imageRegistryCredentials` propriété dans la définition du groupe de conteneurs :

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Pour éviter de stocker le mot de passe de votre registre de conteneurs directement dans le modèle, nous vous recommandons de le stocker en tant que secret dans [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) et de le référencer dans le modèle à l’aide de l’ [intégration native entre Azure Resource Manager et Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Déploiement avec le Portail Azure

Si vous gérez des images de conteneur dans le registre Azure Container Registry, vous pouvez facilement créer un conteneur dans Azure Container Instances via le portail Azure.

1. Dans le portail Azure, accédez à votre registre de conteneurs.

2. Sélectionnez **Référentiels**, puis le référentiel dont proviendra le déploiement, cliquez avec le bouton droit sur la balise de l’image conteneur que vous souhaitez déployer et sélectionnez **Exécuter l’instance**.

    ![« Exécuter l’instance » dans Azure Container Registry sur le Portail Azure][acr-runinstance-contextmenu]

3. Entrez un nom pour le conteneur et un nom pour le groupe de ressources. Vous pouvez également modifier les valeurs par défaut si vous le souhaitez.

    ![Créer un menu pour Azure Container Instances][acr-create-deeplink]

4. Une fois le déploiement terminé, vous pouvez naviguer vers le groupe de conteneurs à partir du panneau Notifications pour trouver son adresse IP et autres propriétés.

    ![Affichage des détails pour le groupe de conteneurs d’Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>étapes suivantes

Découvrez comment créer des conteneurs, les envoyer (push) à un Registre de conteneurs privé et les déployer sur Azure Container Instances en [effectuant le didacticiel](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create