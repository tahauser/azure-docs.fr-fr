---
title: "Surveiller le cluster DC/OS Azure – Dynatrace"
description: "Surveillez un cluster DC/OS Azure Container Service avec Dynatrace. Déployez Dynatrace OneAgent à l’aide du tableau de bord DC/OS."
services: container-service
author: MartinGoodwell
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 52a8a05f050017f5062ff986bad7ec2e6a650aa5
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>Surveiller un cluster DC/OS Azure Container Service avec Dynatrace SaaS/Managed

Dans cet article, nous vous montrons comment déployer [Dynatrace](https://www.dynatrace.com/) OneAgent pour surveiller tous les nœuds d’agents de votre cluster Azure Container Service. Vous avez besoin d’un compte Dynatrace SaaS/Managé pour cette configuration. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managé
Dynatrace est une solution de surveillance cloud native pour environnements de clusters et de conteneurs hautement dynamiques. Elle vous permet de mieux optimiser les déploiements de conteneurs et les allocations de mémoire à l’aide de données d’utilisation en temps réel. Elle est capable d’identifier automatiquement les problèmes d’infrastructure et d’applications en fournissant une planification automatisée, une corrélation des problèmes et une détection des causes racines.

La figure suivante illustre l’interface utilisateur de Dynatrace :

![IU de Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Composants requis 
[Déployez](container-service-deployment.md) et [connectez](./../container-service-connect.md) sur un cluster configuré par Azure Container Service. Explorez [l’interface utilisateur Marathon](container-service-mesos-marathon-ui.md). Accédez à [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) pour configurer un compte Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configurer un déploiement Dynatrace avec Marathon
Ces étapes vous expliquent comment configurer et déployer des applications Dynatrace dans votre cluster avec Marathon.

1. Accédez à l'interface utilisateur de votre contrôleur de domaine/système d’exploitation via [http://localhost:80/](http://localhost:80/). Une fois dans l’interface utilisateur de DC/OS, accédez à l’onglet **Univers**, puis recherchez **Dynatrace**.

    ![Dynatrace dans l’Univers DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Pour terminer la configuration, vous avez besoin d’un compte Dynatrace SaaS ou d’un compte d’essai gratuit. Une fois dans le tableau de bord Dynatrace, sélectionnez **Déployer Dynatrace**.

    ![Configurer l’intégration PaaS Dynatrace](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Sur la page, sélectionnez **Configurer l’intégration PaaS**. 

    ![Jeton API Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Entrez votre jeton API dans la configuration de Dynatrace OneAgent au sein de l’Univers DC/OS. 

    ![Configuration de Dynatrace OneAgent dans l’Univers DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Donnez aux instances la valeur du nombre de nœuds que vous envisagez d’exécuter. Il est également possible de définir un nombre plus élevé, mais DC/OS essayera de trouver de nouvelles instances jusqu’à atteindre ce nombre. Si vous préférez, vous pouvez également fixer une valeur comme 1000000. Dans ce cas, chaque fois qu’un nouveau nœud est ajouté au cluster, Dynatrace déploie automatiquement un agent sur ce nouveau nœud, l’inconvénient étant que DC/OS essaye constamment de déployer d’autres instances.

    ![Configuration de Dynatrace dans l’Univers DC/OS : instances](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez installé le package, revenez au tableau de bord Dynatrace. Vous pouvez explorer les différentes métriques d’utilisation des conteneurs de votre cluster. 