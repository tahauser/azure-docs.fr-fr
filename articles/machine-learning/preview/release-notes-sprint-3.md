---
title: Notes de publication d’Azure Machine Learning Workbench pour la version sprint 3 de janvier 2018
description: Ce document décrit en détail les mises à jour pour la version sprint 3 d’Azure Machine Learning
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: fa209ba2259ae82796556fc1229cd6944c7a2ae1
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="sprint-3---january-2018"></a>Sprint 3 - janvier 2018 

#### <a name="version-number-01171218263"></a>Numéro de version : 0.1.1712.18263

>Voici comment vous pouvez [trouver le numéro de version](known-issues-and-troubleshooting-guide.md).

Bienvenue dans la quatrième mise à jour d’Azure Machine Learning Workbench. Voici les mises à jour et les améliorations de cette version sprint. La plupart de ces mises à jour sont les résultats directs des commentaires des utilisateurs. 

## <a name="notable-new-features-and-changes"></a>Nouvelles fonctionnalités et modifications notables
- Les mises à jour de la pile d’authentification obligent à sélectionner un compte et un identifiant au démarrage

## <a name="detailed-updates"></a>Mises à jour détaillées
Vous trouverez ci-dessous une liste des mises à jour détaillées de chaque zone de composant d’Azure Machine Learning dans ce sprint.

### <a name="workbench"></a>Workbench
- Possibilité d’installer/de désinstaller l’application à partir de l’assistant d’ajout/suppression de programmes
- Les mises à jour de la pile d’authentification obligent à sélectionner un compte et un identifiant au démarrage
- Amélioration de l’expérience d’authentification unique (SSO) sur Windows
- Les utilisateurs qui appartiennent à plusieurs clients avec différentes informations d’identification pourront désormais se connecter à Workbench

#### <a name="ui"></a>Interface utilisateur
- Améliorations générales et résolution de bogues

### <a name="notebooks"></a>Blocs-notes
- Améliorations générales et résolution de bogues

### <a name="data-preparation"></a>Préparation des données 
- Amélioration des suggestions automatiques lors de l’exécution de transformations Par l’exemple
- Amélioration de l’algorithme pour l’inspecteur de fréquence de motifs
- Possibilité d’envoyer des exemples de commentaires et de données lors de l’exécution de transformations Par l’exemple ![Image du lien d’envoi de commentaires lors de la dérivation d’une colonne](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Améliorations apportées au runtime Spark
- Scala a remplacé Pyspark
- Élimination de l’incapacité à fermer les données non applicables à Time Series Inspector 
- Résolution du blocage de l’heure d’exécution de la préparation des données pour HDI

### <a name="model-management-cli-updates"></a>Mises à jour de l’interface de commande de gestion des modèles 
  - La propriété de l’abonnement n’est plus nécessaire pour l’approvisionnement des ressources. L’accès collaborateur au groupe de ressources est suffisant pour configurer l’environnement de déploiement.
  - Autorisation de configuration de l’environnement local pour les abonnements gratuits 
