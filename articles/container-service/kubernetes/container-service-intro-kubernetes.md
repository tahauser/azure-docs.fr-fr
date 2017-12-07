---
title: "Présentation d’Azure Container Service pour Kubernetes"
description: "Azure Container Service pour Kubernetes simplifie le déploiement et la gestion des applications en conteneur dans Azure."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 1a877204e91098191c218bf1c991cc5d8e53ee7a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="introduction-to-azure-container-service-for-kubernetes"></a>Présentation d’Azure Container Service pour Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Azure Container Service pour Kubernetes simplifie la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur. Il vous permet d’exploiter vos compétences existantes ou de faire appel à une large communauté d’experts toujours plus nombreux pour déployer et gérer des applications en conteneur sur Microsoft Azure.

En utilisant Azure Container Service, vous pouvez tirer parti des fonctionnalités d’entreprise d’Azure tout en conservant la portabilité des applications par le biais de Kubernetes et du format d’image Docker.

## <a name="using-azure-container-service-for-kubernetes"></a>Utilisation d’Azure Container Service pour Kubernetes
L’objectif d’Azure Container Service est de proposer un environnement d’hébergement de conteneurs basé sur des outils et des technologies open source déjà bien connus de nos clients. Dans cette optique, nous présentons les points de terminaison standards de l’API Kubernetes. En utilisant ces points de terminaison standards, vous pouvez exploiter n’importe quel logiciel capable de communiquer avec un cluster Kubernetes. Par exemple, vous pourriez choisir [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/), ou [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Création d’un cluster Kubernetes à l’aide d’Azure Container Service
Pour commencer à utiliser Azure Container Service, déployez un cluster Azure Container Service à l’aide d’[Azure CLI 2.0](container-service-kubernetes-walkthrough.md) ou via le portail (recherchez **Azure Container Service** dans le Marketplace). Si vous êtes un utilisateur expérimenté ayant désireux d’avoir davantage de contrôle sur les modèles Azure Resource Manager, vous pouvez utiliser le projet open source [acs-engine](https://github.com/Azure/acs-engine) pour générer votre propre cluster Kubernetes personnalisé et le déployer via `az` CLI.

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

Diagramme architectural de Kubernetes déployé via Azure Container Service :

![Azure Container Service configuré pour utiliser Kubernetes.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>Vidéos

Prise en charge de Kubernetes dans Azure Container Service (Azure Friday janvier 2017) :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Outils dédiés au développement et au déploiement d’applications sur Kubernetes (Azure OpenDev juin 2017) :

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>Étapes suivantes

Explorez le [Guide de démarrage rapide de Kubernetes](container-service-kubernetes-walkthrough.md) pour commencer à explorer Azure Container Service dès aujourd’hui.