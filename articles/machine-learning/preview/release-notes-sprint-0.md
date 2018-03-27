---
title: "Notes de publication d’Azure Machine Learning Workbench pour la version sprint 0 d’octobre 2017"
description: "Ce document décrit en détail les mises à jour pour la version sprint 0 d’Azure Machine Learning"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>Sprint 0 - Octobre 2017 

**Numéro de version : 0.1.1710.04013**

Bienvenue dans la première mise à jour d’Azure Machine Learning Workbench. Celle-ci fait suite à la préversion publique dévoilée lors de la conférence Microsoft Ignite 2017. La mise à jour regroupe principalement des correctifs visant à améliorer la fiabilité et la stabilité.  Voici quelques-uns des problèmes critiques que nous avons résolus :

## <a name="new-features"></a>Nouvelles fonctionnalités
- macOS High Sierra est désormais pris en charge

## <a name="bug-fixes"></a>Résolution des bogues
### <a name="workbench-experience"></a>Expérience dans Workbench
- Le fait de glisser-déplacer un fichier dans Workbench entraîne le blocage de Workbench.
- La fenêtre de terminal dans VS Code configuré comme IDE pour Workbench ne reconnaît pas les commandes _az ml_.

### <a name="workbench-authentication"></a>Authentification Workbench
Nous avons apporté un certain nombre de modifications pour résoudre plusieurs problèmes qui nous ont été signalés en matière de connexion et d’authentification.
- La fenêtre d’authentification s’ouvre à plusieurs reprises, en particulier quand la connexion Internet n’est pas stable.
- Amélioration des problèmes de fiabilité liés à l’expiration des jetons d’authentification.
- Dans certains cas, la fenêtre d’authentification apparaît deux fois.
- La fenêtre principale de Workbench affiche le message « authentification » alors que le processus d’authentification est terminé et que la boîte de dialogue contextuelle est fermée.
- Si aucune connexion Internet n’est établie, la boîte de dialogue d’authentification s’ouvre avec un écran vide.

### <a name="data-preparation"></a>Préparation des données 
- Quand une valeur spécifique est filtrée, les erreurs et les valeurs manquantes sont également exclues.
- Le fait de changer une stratégie d’échantillonnage supprime les opérations de jointure existantes suivantes.
- Le remplacement d’une transformation Valeur manquante ne tient pas compte de NaN.
- L’inférence de type de date lève une exception quand la valeur null est rencontrée.

### <a name="job-execution"></a>Exécution des tâches
- Aucun message d’erreur clair ne s’affiche quand l’exécution d’une tâche ne parvient pas à charger un dossier de projet qui dépasse la taille limite.
- Si le script Python de l’utilisateur change le répertoire de travail, les fichiers écrits dans les dossiers de sortie ne sont pas suivis. 
- Si l’abonnement Azure actif est différent de celui auquel le projet actuel appartient, la soumission de la tâche entraîne une erreur 403.
- Quand Docker n’est pas présent, aucun message d’erreur clair n’est retourné si l’utilisateur tente d’utiliser Docker comme cible d’exécution.
- Le fichier .runconfig n’est pas enregistré automatiquement quand l’utilisateur clique sur le bouton _Exécuter_.

### <a name="jupyter-notebook"></a>Jupyter Notebook
- Le serveur Notebook ne peut pas démarrer si l’utilisateur utilise certains types de connexion.
- Les messages d’erreur du serveur Notebook ne figurent pas dans les journaux accessibles à l’utilisateur.

### <a name="azure-portal"></a>Portail Azure
- Si le thème sombre du portail Azure est sélectionné, une boîte noire apparaît à la place du panneau Gestion des modèles.

### <a name="operationalization"></a>Opérationnalisation
- La réutilisation d’un manifeste pour mettre à jour un service web entraîne la génération d’une nouvelle image Docker avec un nom aléatoire.
- Les journaux du service web ne peuvent pas être extraits à partir d’un cluster Kubernetes.
- Un message d’erreur trompeur est imprimé quand un utilisateur se heurte à des problèmes d’autorisation durant une tentative de création d’un compte Gestion des modèles ou Machine Learning Compute.
