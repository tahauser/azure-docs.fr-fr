---
title: Présentation d’Azure Container Service pour Kubernetes
description: Azure Container Service pour Kubernetes simplifie le déploiement et la gestion des applications en conteneur dans Azure.
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 5bfa445eb11ed8be608278d0b95249372f9976ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-container-service-aks-preview"></a>Présentation de la préversion d’Azure Container Service (AKS)

Azure Container Service (AKS) simplifie la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur. Il vous permet d’exploiter vos compétences existantes ou de faire appel à une large communauté d’experts toujours plus nombreux pour déployer et gérer des applications en conteneur sur Microsoft Azure.

En utilisant AKS, vous pouvez tirer parti des fonctionnalités d’entreprise d’Azure tout en conservant la portabilité des applications par le biais de Kubernetes et du format d’image Docker.

> [!IMPORTANT]
> Azure Container Service (AKS) est actuellement en **préversion**. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.
>

## <a name="managed-kubernetes-in-azure"></a>Managed Kubernetes dans Azure

AKS permet de réduire la complexité et la surcharge opérationnelle de la gestion d’un cluster Kubernetes en déléguant une grande partie de cette responsabilité à Azure. En tant que service Kubernetes hébergé, Azure gère pour vous des tâches critiques telles que l’analyse de l'intégrité et la maintenance. En outre, vous payez uniquement pour les nœuds de l’agent au sein de vos clusters, pas pour les maîtres. En tant que service Kubernetes géré, AKS fournit :

> [!div class="checklist"]
> * Des mises à niveau de version et des correctifs Kubernetes automatisés
> * Une mise à l’échelle de cluster simplifiée
> * Un plan de contrôle hébergé à réparation spontanée (maîtres)
> * Des coûts réduits : payez uniquement pour les nœuds du pool de l'agent en cours d'exécution

Comme Azure gère la gestion des nœuds de votre cluster AKS, vous n’avez plus besoin d'effectuer les tâches manuellement, notamment les mises à niveau du cluster. Comme Azure gère ces tâches de maintenance critiques pour vous, AKS ne fournit pas d'accès direct (comme avec SSH) au cluster.

## <a name="using-azure-container-service-aks"></a>Utilisation d’Azure Container Service (AKS)
L’objectif d’AKS est de proposer un environnement d’hébergement de conteneurs basé sur des outils et des technologies open source déjà bien connus de nos clients. Dans cette optique, nous présentons les points de terminaison standards de l’API Kubernetes. En utilisant ces points de terminaison standards, vous pouvez exploiter n’importe quel logiciel capable de communiquer avec un cluster Kubernetes. Par exemple, vous pourriez choisir [kubectl][kubectl-overview], [helm][helm] ou [draft][draft].

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Création d’un cluster Kubernetes à l’aide d’Azure Container Service (AKS)
Pour commencer à utiliser AKS, déployez un cluster AKS avec la [CLI d’Azure][aks-quickstart] ou via le portail (recherchez **Azure Container Service** sur le marketplace). Si vous êtes un utilisateur expérimenté désireux d’avoir davantage de contrôle sur les modèles Azure Resource Manager, vous pouvez utiliser le projet open source [acs-engine][acs-engine] pour générer votre propre cluster Kubernetes personnalisé et le déployer via la CLI `az`.

### <a name="using-kubernetes"></a>Utilisation de Kubernetes
Kubernetes automatise le déploiement, la mise à l’échelle et la gestion des applications en conteneur. Il possède un jeu complet de fonctionnalités, notamment :
* Binpacking automatique
* Réparation spontanée
* Mise à l’échelle horizontale
* Détection de service et équilibrage de charge
* Déploiements et restaurations automatisés
* Secret et gestion de la configuration
* Orchestration de stockage
* Exécution Batch

## <a name="videos"></a>vidéos

Azure Container Service (AKS) - Azure Friday, octobre 2017 :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Outils dédiés au développement et au déploiement d’applications sur Kubernetes (Azure OpenDev, juin 2017) :

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

En savoir plus sur le déploiement et la gestion des ACS avec ACS démarrage rapide.

> [!div class="nextstepaction"]
> [Didacticiel ACS][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

