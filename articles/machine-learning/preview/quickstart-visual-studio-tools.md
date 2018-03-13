---
title: "Article de démarrage rapide pour Visual Studio Tools pour Machine Learning sur Azure | Microsoft Docs"
description: "Cet article décrit comment prendre en main Visual Studio Tools pour Machine Learning, entre la création d’une expérimentation, l’apprentissage d’un modèle et la mise en place d’un service web."
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
ms.openlocfilehash: bbcb2ea5a7ceeb976f590393608cc29c67d9a49e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools pour IA
Visual Studio Tools pour IA est une extension de développement permettant de générer, tester et déployer des solutions d’intelligence artificielle et d’apprentissage profond. Il propose une intégration transparente à Azure Machine Learning, notamment un affichage de l’historique des exécutions, détaillant les performances des formations précédentes et des métriques personnalisées. Il présente un affichage de l’explorateur d’exemples, ce qui permet de parcourir et de démarrer de nouveaux projets avec [Microsoft Cognitive Toolkit (anciennement appelé CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) et d’autres frameworks d’apprentissage profond. Enfin, il fournit un explorateur pour les cibles de calcul, ce qui vous permet d’envoyer des tâches pour effectuer l’apprentissage de modèles dans des environnements distants comme les machines virtuelles Azure ou les serveurs Linux avec GPU. Il simplifie également l’accès à [Azure Batch AI (version préliminaire)](https://docs.microsoft.com/azure/batch-ai/).
 
## <a name="getting-started"></a>Prise en main 
Pour commencer, vous devez d’abord télécharger et installer [Visual Studio](https://www.visualstudio.com/downloads/). Une fois que Visual Studio est ouvert, effectuez les étapes suivantes :
1. Cliquez sur la barre de menus dans Visual Studio, puis sur « Extensions et mises à jour... »
2. Cliquez sur l’onglet « En ligne » puis sur « Chercher dans Visual Studio Marketplace ».
3. Recherchez « Visual Studio Tools pour IA ». 
3. Cliquez sur le bouton **Download** . 
4. Redémarrez Visual Studio après l’installation. 

Une fois que Visual Studio Code est rechargé, l’extension est active. [En savoir plus sur la recherche des extensions](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> Visual Studio Tools pour IA nécessite Visual Studio édition 2015 ou 2017, Professional ou Enterprise. La version Apple OSX n’est pas prise en charge. 


## <a name="exploring-project-samples"></a>Exploration des exemples de projets
Visual Studio Tools pour IA est fourni avec un explorateur d’exemples. L’explorateur d’exemples facilite la détection des exemples et leur test en seulement quelques clics. Pour ouvrir l’explorateur, effectuez les étapes suivantes :   
1. Dans la barre de menus, cliquez sur **Outils IA**.
2. Cliquez sur « Galerie Machine Learning ».

Un onglet avec tous les exemples Azure ML s’ouvre.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Création d’un projet à partir de l’explorateur d’exemples 
Vous pouvez parcourir différents exemples et obtenir plus d’informations à leur sujet. Partons à la recherche de l’exemple « Classifying Iris » (Classification d’iris). Pour créer un projet basé sur cet exemple, effectuez les étapes suivantes :
1. Cliquez sur le bouton **Installer** sur l’exemple de projet. Une nouvelle boîte de dialogue apparaît. 
2. Sélectionnez un groupe de ressources, un compte et un espace de travail.
3. Vous pouvez laisser le type de projet en Général.
4. Entrez un chemin de projet et un nom de projet, puis appuyez sur Entrée. 
5. Une boîte de dialogue s’ouvre invitant l’utilisateur à enregistrer une solution. Cliquez sur Enregistrer. 

Une fois terminé, le nouveau projet s’ouvre dans une nouvelle instance de Visual Studio. 

> [!TIP]
> Vous devez être connecté pour accéder à votre ressource Azure. À partir du terminal incorporé, entrez « az login » et suivez les instructions. 

## <a name="submitting-experiment-with-the-new-project"></a>Envoi de l’expérimentation avec le nouveau projet
Le nouveau projet étant ouvert dans Visual Studio Code, soumettez une tâche à une cible de calcul (en local ou machine virtuelle avec docker).
Pour envoyez la tâche, procédez comme suit : 
1. Depuis l’Explorateur de solutions, cliquez sur le bouton droit de la souris sur le fichier que vous souhaitez envoyer, et sélectionnez **Définir comme fichier de démarrage**.
2. Sélectionnez le nom du projet, cliquez sur le bouton droit de la souris et sélectionnez **Envoyez la tâche...**
3. Une nouvelle boîte de dialogue apparaît. Choisissez le cluster (ou la cible de calcul) pour exécuter votre script.
4. Cliquez sur **Envoyer**.

Une fois que la tâche est envoyée, le terminal incorporé affiche la progression des exécutions.

## <a name="view-list-of-jobs"></a>Afficher la liste des tâches
Une fois que la tâche est envoyée, vous pouvez répertorier les tâches à partir de l’historique des exécutions.
1. Dans l’**Explorateur de serveurs**, cliquez sur **Outils IA**.
2. Sélectionnez ensuite **Azure Machine Learning**.
3. Cliquez sur le menu **Tâches**.

L’Explorateur de tâches répertorie toutes les expériences envoyées pour ce projet. 

## <a name="view-job-details"></a>Affichage des détails d’une tâche
Avec l’affichage de l’Explorateur de tâches ouvert, cliquez sur la première exécution de la liste.
Cela chargera le panneau Résumé de la tâche et le panneau Journaux et résultats.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Guide pratique pour configurer Azure Machine Learning afin qu’il fonctionne avec un IDE](./how-to-configure-your-IDE.md)
