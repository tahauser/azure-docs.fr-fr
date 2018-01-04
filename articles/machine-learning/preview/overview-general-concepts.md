---
title: "Présentation conceptuelle des fonctionnalités en préversion d’Azure Machine Learning | Microsoft Docs"
description: "Présentation conceptuelle des fonctionnalités en préversion d’Azure Machine Learning, tels que les abonnements, les comptes, les espaces de travail, les projets, etc."
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: ea9da6f23fd08c09f9e805519487648480816f35
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning - Concepts

Cet article définit et décrit les concepts à connaître pour utiliser Azure Machine Learning. 

![Hiérarchie des concepts](media/overview-general-concepts/hierarchy.png)

- **Abonnement :** un abonnement Azure vous permet d’accéder aux ressources dans Azure. Azure Machine Learning étant parfaitement intégré au calcul, au stockage et à de nombreuses autres ressources et services Azure, Workbench exige que chaque utilisateur ait accès à un abonnement Azure valide. Les utilisateurs doivent également disposer d’autorisations suffisantes dans cet abonnement pour créer des ressources.


- **Compte d’expérimentation :** un compte d’expérimentation est une ressource Azure indispensable à Azure ML, et un véhicule de facturation. Il contient vos espaces de travail qui, à leur tour, contiennent des projets. Vous pouvez ajouter plusieurs utilisateurs, appelés _sièges_, à un compte d’expérimentation. Vous devez avoir accès à un compte d’expérimentation pour utiliser Azure ML Workbench et exécuter des expérimentations. 


- **Compte de gestion des modèles :** un compte de gestion des modèles est également une ressource Azure nécessaire à Azure ML pour gérer des modèles. Vous pouvez l’utiliser pour inscrire des manifestes et des modèles, créer des services web en conteneur et les déployer localement ou dans le cloud. C’est également l’autre véhicule de facturation d’Azure ML.


- **Espace de travail :** un espace de travail est le principal composant du partage et de la collaboration dans Azure ML. Les projets sont regroupés dans un espace de travail. Un espace de travail peut ensuite être partagé entre plusieurs utilisateurs qui ont été ajoutés au compte d’expérimentation.


- **Projet :** dans Azure Machine Learning, un projet représente le conteneur logique pour tout le travail effectué en vue de résoudre un problème. Il est mappé à un seul dossier de fichiers sur votre disque local, et vous pouvez y ajouter des fichiers et des sous-dossiers. Un projet peut éventuellement être associé à un dépôt Git pour le contrôle de code source et la collaboration.  

- **Expérimentation :** dans Azure ML, une expérimentation désigne un ou plusieurs fichiers de code source pouvant être exécutés à partir d’un point d’entrée unique. Elle peut contenir des tâches telles que l’ingestion de données, l’ingénierie de caractéristiques, la formation de modèles ou l’évaluation de modèles. Actuellement, Azure ML ne prend en charge que les expérimentations Python ou PySpark.


- **Modèle :** dans Azure Machine Learning, les modèles font référence au produit d’une expérimentation d’apprentissage automatique. Ce sont des recettes qui, quand elles sont appliquées correctement à des données, génèrent des valeurs prédites. Les modèles peuvent être déployés sur des environnements de test ou de production et utiliser pour calculer le score des nouvelles données. Lorsqu’ils sont en production, les modèles peuvent être surveillés au niveau de leurs performances et de la dérive des données, et reformés si nécessaire. 

- **Cible de calcul :** une cible de calcul constitue la ressource de calcul que vous configurez pour l’exécution de vos expérimentations. Ce peut être votre ordinateur local (Windows ou macOS), le conteneur Docker s’exécutant sur votre ordinateur local ou dans une machine virtuelle Linux sur Azure, ou un cluster HDInsight Spark.


- **Exécution :** le service d’expérimentation définit une exécution comme étant la durée de vie de l’exécution d’une expérimentation dans une cible de calcul. Azure ML capture automatiquement les informations de chaque exécution et présente l’historique complet d’une expérimentation spécifique sous la forme d’un historique d’exécution.

- **Environnement :** dans Azure Machine Learning, un environnement désigne une ressource informatique particulière qui est utilisée pour déployer et gérer vos modèles. Ce peut être votre ordinateur local, une machine virtuelle Linux sur Azure ou un cluster Kubernetes s’exécutant dans Azure Container Service, selon le contexte et la configuration. Votre modèle est hébergé dans un conteneur Docker fonctionnant dans ces environnements et est exposé comme point de terminaison d’API REST.


- **Modèle géré :** la gestion des modèles vous permet de déployer des modèles en tant que services web, de gérer les différentes versions de vos modèles et de suivre leurs performances ainsi que leurs métriques. Les modèles gérés sont inscrits auprès d’un compte Gestion des modèles Azure Machine Learning.

- **Manifestes :** lorsque le système de Gestion des modèles déploie un modèle en production, il inclut un manifeste qui peut englober le modèle, les dépendances, le script de scores, les exemples de données et le schéma. Le manifeste est la recette utilisée pour créer une image de conteneur Docker. À l’aide de la Gestion des modèles, vous pouvez générer automatiquement les manifestes, créer différentes versions et gérer ces manifestes. 


- **Images :** vous pouvez utiliser les manifestes pour générer (et regénérer) des images Docker. Les images Docker en conteneur offre une certaine flexibilité pour les exécuter dans le cloud, sur des machines locales ou des appareils IoT. Les images sont autonomes et incluent toutes les dépendances nécessaires au calcul du score des nouvelles données avec des modèles. 

- **Services :** la Gestion des modèles vous permet de déployer des modèles en tant que services web. La logique de service web et les dépendances sont encapsulées dans une image. Chaque service web est un ensemble de conteneurs basé sur l’image prête à traiter les demandes sur une URL donnée. Un service web est compté comme un seul déploiement.
