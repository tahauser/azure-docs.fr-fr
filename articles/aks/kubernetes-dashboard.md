---
title: "Gérer le cluster Kubernetes Azure par l’intermédiaire de l’interface utilisateur web"
description: Utilisation du tableau de bord Kubernetes dans ACS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 47608993320d987822422ea103e4e2043de2d2e8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Tableau de bord Kubernetes avec Azure Container Service (ACS)

Vous pouvez utiliser Azure CLI pour démarrer le tableau de bord Kubernetes. Ce document décrit le démarrage du tableau de bord Kubernetes à l’aide d’Azure CLI, ainsi que certaines opérations de base exécutables à partir du tableau de bord. Pour plus d’informations sur le tableau de bord Kubernetes, consultez [Tableau de bord de l’interface utilisateur web de Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans ce document supposent que vous ayez créé un cluster ACS et que vous ayez établi une connexion kubectl avec le cluster. Si vous avez besoin de ces éléments, reportez-vous au [guide de démarrage rapide d’ACS][aks-quickstart].

Vous devez également avoir installé et configuré Azure CLI version 2.0.27 ou version ultérieure. Pour déterminer la version, exécutez la commande az--version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Démarrer le tableau de bord Kubernetes

Pour démarrer le tableau de bord Kubernetes, utilisez la commande `az aks browse`. Lorsque vous exécutez cette commande, remplacez les noms du groupe de ressources et du cluster.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Cette commande crée un proxy entre votre système de développement et l’API Kubernetes, et ouvre un navigateur web sur le tableau de bord Kubernetes.

## <a name="run-an-application"></a>Exécuter une application

Dans le tableau de bord Kubernetes, cliquez sur le bouton **Créer** dans la fenêtre supérieure droite. Nommez le déploiement `nginx`, puis entrez `nginx:latest` pour le nom des images. Sous **Service**, sélectionnez **Externe**, puis entrez `80` pour le port et le port cible.

Lorsque vous êtes prêt, cliquez sur **Déployer** pour créer le déploiement.

![Boîte de dialogue de création de service Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Afficher l’application

L’état de l’application peut être consulté sur le tableau de bord Kubernetes. Une fois l’application en cours d’exécution, une case à cocher verte apparaît en regard de chaque composant.

![Pods kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Pour plus d’informations sur les pods d’application, cliquez sur **Pods** dans le menu de gauche, puis sélectionnez le pod **NGINX**. Vous pouvez voir ici des informations spécifiques du pod, telles que la consommation de ressources.

![Ressources Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Pour trouver l’adresse IP de l’application, cliquez sur **Services** dans le menu de gauche, puis sélectionnez le service **NGINX**.

![Vue nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Modifier l’application

En plus de la création et de l’affichage des applications, le tableau de bord Kubernetes permet de modifier et de mettre à jour des déploiements d’applications.

Pour modifier un déploiement, cliquez sur **Déploiements** dans le menu de gauche, puis sélectionnez le déploiement **NGINX**. Enfin, sélectionnez **Modifier** dans la barre de navigation en haut à droite.

![Modification de Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Recherchez la valeur de `spec.replica`, qui devrait être 1, et modifiez-la en 3. De ce fait, le nombre de réplicas du déploiement NGINX passe de 1 à 3.

Lorsque vous êtes prêt, sélectionnez **Mettre à jour**.

![Modification de Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le tableau de bord Kubernetes, voir la documentation de Kubernetes.

> [!div class="nextstepaction"]
> [Tableau de bord de l’interface utilisateur web de Kubernetes][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli