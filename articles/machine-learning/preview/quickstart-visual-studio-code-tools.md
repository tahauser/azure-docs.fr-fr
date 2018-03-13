---
title: "Article de démarrage rapide pour Visual Studio Code Tools pour Machine Learning sur Azure | Microsoft Docs"
description: "Cet article décrit comment bien démarrer à l’aide de Visual Studio Code Tools pour Machine Learning, entre la création d’une expérimentation, l’apprentissage d’un modèle et la mise en place d’un service web."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: 7ec74dd9901171b7ec62b0bac5bfb1da80fee5ca
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools pour IA
Visual Studio Code Tools pour IA est une extension de développement permettant de générer, tester et déployer des solutions d’intelligence artificielle et d’apprentissage profond. Il propose une intégration transparente à Azure Machine Learning, notamment un affichage de l’historique des exécutions, détaillant les performances des formations précédentes et des métriques personnalisées. Il présente un affichage de l’explorateur d’exemples, ce qui permet de parcourir et de démarrer de nouveaux projets avec [Microsoft Cognitive Toolkit (anciennement appelé CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) et d’autres frameworks d’apprentissage profond. Enfin, il fournit un explorateur pour les cibles de calcul, ce qui vous permet d’envoyer des tâches pour effectuer l’apprentissage de modèles dans des environnements distants comme les machines virtuelles Azure ou les serveurs Linux avec GPU. 
 
## <a name="getting-started"></a>Prise en main 
Pour commencer, vous devez d’abord télécharger et installer [Visual Studio Code](https://code.visualstudio.com/Download). Une fois que Visual Studio Code est ouvert, effectuez les étapes suivantes :
1. Cliquez sur l’icône d’extension dans la barre d’activités. 
2. Recherchez « Visual Studio Code Tools pour IA ». 
3. Cliquez sur le bouton **Installer**. 
4. Après l’installation, cliquez sur le bouton **Recharger**. 

Une fois que Visual Studio Code est rechargé, l’extension est active. [En savoir plus sur l’installation des extensions](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Exploration des exemples de projets
Visual Studio Code Tools pour IA est fourni avec un explorateur d’exemples. L’explorateur d’exemples facilite la détection des exemples et leur test en seulement quelques clics. Pour ouvrir l’explorateur, effectuez les étapes suivantes :   
1. Ouvrez la palette de commandes (View [Affichage] > **Palette de commandes** ou **Ctrl+Maj+P**).
2. Entrez « Exemple IA ». 
3. Vous obtenez une recommandation pour « AI: Open Azure ML Sample Explorer (IA : Ouvrir l’explorateur d’exemples Azure Machine Learning) », sélectionnez-la et appuyez sur Entrée. 

Vous pouvez aussi cliquer sur l’icône représentant l’explorateur d’exemples.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Création d’un projet à partir de l’explorateur d’exemples 
Vous pouvez parcourir différents exemples et obtenir plus d’informations à leur sujet. Partons à la recherche de l’exemple « Classifying Iris » (Classification d’iris). Pour créer un projet basé sur cet exemple, effectuez les étapes suivantes :
1. Cliquez sur le bouton Installer dans l’exemple de projet, notez les commandes demandées qui vous guident au cours des étapes de création d’un projet. 
2. Choisissez un nom pour le projet, par exemple « Iris ».
3. Choisissez un chemin de dossier pour créer votre projet et appuyez sur Entrée. 
4. Sélectionnez un espace de travail existant et appuyez sur Entrée.

Le projet est alors créé.

> [!TIP]
> Vous devez être connecté pour accéder à votre ressource Azure. À partir du terminal incorporé, entrez « az login » et suivez les instructions. 

## <a name="submitting-experiment-with-the-new-project"></a>Envoi de l’expérimentation avec le nouveau projet
Le nouveau projet étant ouvert dans Visual Studio Code, nous envoyons une tâche à une autre cible de calcul (en local et machine virtuelle avec docker).
Visual Studio Code Tools pour IA fournit plusieurs méthodes pour envoyer une expérimentation. 
1. Menu contextuel (clic droit) - **AI: Submit Job** (IA : Envoyer une tâche).
2. Depuis la palette de commandes : « AI: Submit Job » (« IA : Envoyer une tâche »).
3. Vous pouvez également exécuter la commande directement à l’aide de l’interface de ligne de commande Azure, des commandes Machine Learning, du terminal incorporé.

Ouvrez iris_sklearn.py, cliquez avec le bouton droit et sélectionnez **AI: Submit Job** (IA : Envoyer une tâche).
1. Sélectionnez votre plateforme : « Azure Machine Learning ».
2. Sélectionnez la configuration de votre exécution : « Docker-Python ».

> [!NOTE]
> S’il s’agit de la première fois que vous envoyez une tâche, vous recevez un message « No Machine Learning configuration found, creating... (Aucune configuration Machine Learning trouvée, création en cours...) ». Un fichier JSON est ouvert, enregistrez-le (**Ctrl+S**).

Une fois que la tâche est envoyée, le terminal incorporé affiche la progression des exécutions. 

## <a name="view-list-of-jobs"></a>Afficher la liste des tâches
Une fois que les tâches sont envoyées, vous pouvez répertorier les tâches à partir de l’historique des exécutions.
1. Ouvrez la palette de commandes (View [Affichage] > **Palette de commandes** ou **Ctrl+Maj+P**).
2. Entrez « Liste IA ».
3. Vous obtenez une recommandation pour « AI: List Jobs (IA : Répertorier les tâches) », sélectionnez-la et appuyez sur Entrée.

L’affichage de liste des tâches s’ouvre et affiche toutes les exécutions ainsi que des informations associées.

## <a name="view-job-details"></a>Affichage des détails d’une tâche
Avec l’affichage de liste des tâches toujours ouvert, cliquez sur la première exécution de la liste.
Pour obtenir une présentation approfondie des résultats d’une tâche, cliquez sur l’**ID de tâche** supérieur pour afficher des informations détaillées. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Guide pratique pour configurer Azure Machine Learning afin qu’il fonctionne avec un IDE](./how-to-configure-your-IDE.md)
