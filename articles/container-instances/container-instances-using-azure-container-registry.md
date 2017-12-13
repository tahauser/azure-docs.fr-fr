---
title: "Déployer sur Azure Container Instances à partir d’Azure Container Registry"
description: "Déployer Azure Container Instances à partir d’Azure Container Registry"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 9667a5b840d6c1fab5087cfcf3ede34a732fbe01
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Déployer sur Azure Container Instances à partir d’Azure Container Registry

Azure Container Registry est un registre privé Azure pour les images conteneur Docker. Cet article explique comment déployer des images conteneur stockées dans Azure Container Registry sur Azure Container Instances.

## <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

L’interface CLI Azure inclut des commandes pour la création et la gestion des conteneurs dans Azure Container Instances. Si vous spécifiez une image privée dans la commande `create`, vous pouvez également spécifier le mot de passe du Registre d’image nécessaire pour s’authentifier auprès du Registre de conteneurs.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

La commande `create` prend également en charge la spécification de `registry-login-server` et `registry-username`. Toutefois, le serveur de connexion pour le registre Azure Container Registry est toujours *registryname*. azurecr.io et le nom d’utilisateur par défaut est *registryname*, de sorte que ces valeurs sont déduites à partir du nom de l’image si elles ne sont pas fournies de manière explicite.

## <a name="using-an-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

Vous pouvez spécifier les propriétés de votre registre Azure Container Registry dans un modèle Azure Resource Manager en incluant la `imageRegistryCredentials` propriété dans la définition du groupe de conteneurs :

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Pour éviter de stocker le mot de passe de votre registre de conteneurs directement dans le modèle, nous vous recommandons de le stocker en tant que secret dans [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) et de le référencer dans le modèle à l’aide de l’ [intégration native entre Azure Resource Manager et Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Si vous gérez des images de conteneur dans le registre Azure Container Registry, vous pouvez facilement créer un conteneur dans Azure Container Instances via le portail Azure.

1. Dans le portail Azure, accédez à votre registre de conteneurs.

2. Choisissez les référentiels.

    ![Le menu Azure Container Registry dans le portail Azure][acr-menu]

3. Choisissez le référentiel que vous souhaitez déployer.

4. Cliquez avec le bouton droit sur la balise de l’image conteneur que vous souhaitez déployer.

    ![Menu contextuel pour le lancement de conteneurs avec Azure Container Instances][acr-runinstance-contextmenu]

5. Entrez un nom pour le conteneur et un nom pour le groupe de ressources. Vous pouvez également modifier les valeurs par défaut si vous le souhaitez.

    ![Créer un menu pour Azure Container Instances][acr-create-deeplink]

6. Une fois le déploiement terminé, vous pouvez naviguer vers le groupe de conteneurs à partir du panneau Notifications pour trouver son adresse IP et autres propriétés.

    ![Affichage des détails pour le groupe de conteneurs d’Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer des conteneurs, les envoyer (push) à un Registre de conteneurs privé et les déployer sur Azure Container Instances en [effectuant le didacticiel](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
