---
title: Vue d’ensemble de Jenkins et Azure | Microsoft Docs
description: Hébergez la build Jenkins et déployez le serveur Automation dans Azure et utilisez les ressources de calcul et de stockage Azure pour étendre vos pipelines d’intégration et de livraison continues.
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: 08906939ecc80999de94ad7e7141081f959fb6f2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="azure-and-jenkins"></a>Azure et Jenkins

[Jenkins](https://jenkins.io/) est un serveur Automation open source connu utilisé pour configurer l’intégration et la livraison continues pour vos projets de logiciels. Vous pouvez héberger votre déploiement Jenkins sur Azure ou étendre votre configuration Jenkins existante à l’aide des ressources Azure. Des plug-ins Jenkins sont également disponibles pour simplifier l’intégration/la livraison continues de vos applications vers Azure.

Cet article est une présentation de l’utilisation d’Azure avec Jenkins. Il décrit les principales fonctionnalités Azure disponibles pour les utilisateurs de Jenkins. Pour commencer à utiliser votre propre serveur Jenkins dans Azure, consultez notre [démarrage rapide](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Héberger vos serveurs Jenkins dans Azure

Hébergez Jenkins dans Azure pour centraliser l’automatisation de votre build et mettre à l’échelle votre déploiement à mesure que les besoins de vos projets de logiciels augmentent. Vous pouvez déployer Jenkins dans Azure avec ce qui suit :
 
- [Modèle de solution Jenkins](install-jenkins-solution-template.md) dans Place de marché Microsoft Azure.
- [Machines virtuelles Azure](/azure/virtual-machines/linux/overview). Consultez notre [didacticiel](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) pour créer une instance de Jenkins sur une machine virtuelle.
- Sur un cluster Kubernetes qui exécute [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), consultez nos [procédures](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Surveiller et gérer votre déploiement Azure Jenkins à l’aide de [Log Analytics](/azure/log-analytics/log-analytics-overview), d’[Operations Management Suite](/azure/operations-management-suite/operations-management-suite-overview) et d’[Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Mettre à l’échelle l’automatisation de votre build à la demande

Ajoutez des agents de build à votre déploiement Jenkins existant pour mettre à l’échelle la capacité de votre build Jenkins à mesure que le nombre de builds et la complexité de vos travaux et pipelines augmentent. Vous pouvez exécuter ces agents de build sur des machines virtuelles Azure à l’aide du [plug-in Agents de machine virtuelle Azure](jenkins-azure-vm-agents.md). Consultez notre [didacticiel](/azure/jenkins/jenkins-azure-vm-agents) pour plus d’informations.

Une fois configuré avec un [principal du service Azure](/azure/azure-resource-manager/resource-group-overview), les travaux et pipelines Jenkins peuvent utiliser ces informations d’identification pour accomplir ce qui suit :

- Stocker en toute sécurité et archiver des artefacts de build [Stockage Azure](/azure/storage/common/storage-introduction) à l’aide du [plug-in Stockage Azure](https://plugins.jenkins.io/windows-azure-storage). Consultez les [procédures de stockage Jenkins](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) pour en savoir plus.
- Gérer et configurer des ressources Azure avec l’[interface de ligne de commande Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Déployer votre code dans les services Azure

Utilisez les plug-ins Jenkins pour déployer vos applications sur Azure dans le cadre de vos pipelines d’intégration/de livraison continues Jenkins. Le déploiement dans [Azure App Service](/azure/app-service/) et [Azure Container Service](/azure/container-service/kubernetes/) vous permet d’organiser, de tester et de mettre en production des mises à jour de vos applications sans gestion de l’infrastructure sous-jacente.

 Des plug-ins sont disponibles pour être déployés sur les services et environnements suivants :

- [Azure Web App sur Linux](/azure/app-service/containers/app-service-linux-intro). Consultez le [didacticiel](java-deploy-webapp-tutorial.md) pour commencer.
- [Azure Web App](/azure/app-service/app-service-web-overview). Consultez les [procédures](deploy-Jenkins-app-service-plugin.md) pour commencer.

