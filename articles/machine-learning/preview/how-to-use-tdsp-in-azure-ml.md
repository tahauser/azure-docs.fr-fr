---
title: "Structurer des projets avec le modèle de processus TDSP (Team Data Science Process) | Microsoft Docs"
description: "Guide pratique pour instancier des modèles de processus TDSP (Team Data Science Process) dans Azure Machine Learning qui structurent des projets à des fins de collaboration"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: 0f7a6d0a58e2dcfd94e20a81ae12b42f238016c8
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Structurer des projets avec le modèle de processus TDSP (Team Data Science Process)

Ce document fournit des instructions sur la création de projets de science des données dans Azure Machine Learning avec des modèles de processus TDSP. Ces modèles permettent de structurer des projets à des fins de collaboration et de reproductibilité. 


## <a name="what-is-the-team-data-science-process"></a>Qu’est-ce que le processus Team Data Science Process ?
Le processus TDSP (Team Data Science Process) est un processus de science des données agile et itératif qui permet d’exécuter et de proposer des solutions d’analyse avancées. Il est conçu pour améliorer la collaboration et l’efficacité des équipes de science des données au sein des entreprises. Il tient ces objectifs avec quatre composants clés :

   * Une définition du [cycle de vie de la science des données](../team-data-science-process/lifecycle.md) standard.
   * Une structure de projet normalisée, ainsi que des [modèles de documentation et de rapports de projet](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Une infrastructure et des ressources pour l’exécution du projet, comme une infrastructure de calcul et de stockage et des référentiels de code, respectivement.
   * Des [outils et utilitaires](https://github.com/Azure/Azure-TDSP-Utilities) pour les tâches du projet de science des données, telles que :
      - Gestion de version collaborative
      - Révision du code
      - Exploration et modélisation des données
      - Planification du travail

Pour obtenir une description plus complète du processus TDSP, consultez [Vue d’ensemble du processus TDSP (Team Data Science Process)](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Pourquoi utiliser la structure et les modèles de processus TDSP ?
La normalisation de la structure, du cycle de vie et de la documentation des projets de science des données est primordiale pour favoriser une collaboration efficace au sein des équipes de science des données. Créez des projets Machine Learning avec le modèle TDSP afin de fournir l’infrastructure nécessaire à un travail d’équipe coordonné.

Nous avons précédemment publié un [référentiel GitHub pour la structure et les modèles du projet TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) afin de vous aider à atteindre ces objectifs. Mais il n’était pas possible, avant aujourd’hui, d’instancier la structure et les modèles TDSP dans un outil de science des données. Il est désormais possible de créer un projet Machine Learning qui instancie la structure et les modèles de documentation TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>Points à noter avant de créer un projet
*Avant* de créer un projet, passez en revue les éléments suivants :
* Examinez le [modèle](https://aka.ms/tdspamlgithubrepo) Machine Learning TDSP.
* La taille du contenu (autre que celui déjà présent dans le dossier « docs ») doit être inférieure à 25 Mo. Consultez la remarque qui suit cette liste.
* Le dossier sample\_data est uniquement destiné aux petits fichiers de données (de taille inférieure à 5 Mo) avec lesquels vous pouvez tester votre code ou démarrer les premières phases du développement.
* Le stockage de certains fichiers, tels que des fichiers de type Word et PowerPoint, peut augmenter sensiblement la taille du dossier « docs ». Nous vous conseillons de trouver un Wiki collaboratif, une ressource [SharePoint](https://products.office.com/en-us/sharepoint/collaboration) ou toute autre ressource collaborative pour stocker ces fichiers.
* Pour plus d’informations sur le traitement des fichiers volumineux et des sorties dans Machine Learning, consultez l’article [Persistance des modifications et traitement de fichiers volumineux](http://aka.ms/aml-largefiles).

> [!NOTE]
> En dehors du fichier readme.md, tout le contenu lié à la documentation (texte, fichiers Markdown, images et autres fichiers de document) qui n’est *pas* utilisé pendant l’exécution du projet doit résider dans le dossier nommé « docs » (tout en minuscules). Le dossier « docs » est un dossier spécial ignoré par l’exécution de Machine Learning afin que le contenu de ce dossier ne soit pas copié dans les cibles de calcul inutilement. En outre, les objets figurant dans ce dossier ne sont pas pris en compte dans la limite de 25 Mo appliquée à la taille du projet. Par exemple, le dossier « docs » est l’emplacement de stockage des fichiers images volumineux requis dans la documentation. Ces fichiers font malgré tout l’objet d’un suivi par Git par le biais de l’historique des exécutions. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Instancier la structure et les modèles TDSP à partir de la galerie de modèles Machine Learning
Pour créer un projet avec la structure et les modèles de documentation de processus TDSP, exécutez les procédures ci-après.

### <a name="create-a-new-project"></a>Création d'un projet
Pour créer un projet, ouvrez Azure Machine Learning. Sous **Projets** dans le volet supérieur gauche, sélectionnez le signe plus (**+**), puis sélectionnez **Nouveau projet**.

![Nouveau projet](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Créer un projet à structure TDSP
   1. Spécifiez les paramètres et les informations dans la zone ou liste appropriée :

      - Nom du projet
      - Répertoire du projet
      - Description du projet
      - Chemin de dépôt Git vide
      - Nom de l’espace de travail

   2. Ensuite, dans la zone **Rechercher**, entrez **TDSP**. 
   3. Lorsque l’option **Structurer un projet avec TDSP** apparaît, sélectionnez ce modèle. 
   4. Sélectionnez le bouton **Créer** pour créer votre projet avec une structure TDSP. Si vous fournissez un référentiel Git vide lorsque vous créez le projet (dans la zone de texte appropriée), ce référentiel est rempli avec la structure et le contenu du projet une fois le projet créé.

![Créer un projet TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Examiner la structure du projet TDSP
Une fois votre projet créé, vous pouvez en examiner la structure (consultez le panneau gauche dans la figure ci-après). Cette structure contient tous les aspects de la documentation normalisée pour la présentation de l’entreprise. Ces éléments comprennent les étapes du cycle de vie TDSP, l’emplacement des données, les définitions, ainsi que l’architecture de ce modèle de documentation. 

La structure affichée est dérivée de la structure TDSP publiée dans l’article [TDSP project structure, documents, and artifact templates](https://github.com/Azure/Azure-TDSP-ProjectTemplate) (Structure, documents et modèles d’artefact de projet TDSP), avec certaines modifications. Par exemple, plusieurs des modèles de document sont fusionnés dans un seul fichier Markdown, à savoir, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Structure de dossiers du projet
Le modèle de projet TDSP contient les dossiers de premier niveau suivants :
   - **code** : contient le code.
   - **docs** : contient la documentation nécessaire concernant le projet (par exemple, fichiers Markdown et médias associés).
   - **sample_data** : contient des **(petits) EXEMPLES** de données que vous pouvez utiliser dans le cadre du développement initial ou à des fins de test. En règle générale, ces ensembles de données n’excèdent pas quelques Mo (5). Ce dossier ne convient pas à des jeux de données complets ou volumineux.

![Exemples de données](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Utiliser la structure et les modèles de processus TDSP
Vous devez remplir la structure et les modèles avec des informations propres au projet. Vous êtes censé remplir ces éléments avec le code et les informations nécessaires à l’exécution et à la remise de votre projet. Le fichier [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) est un modèle que vous devez modifier directement à l’aide des informations concernant votre projet. Il est fourni avec un ensemble de questions qui vous aident à remplir les informations pour chacune des quatre phases du [cycle de vie du processus TDSP](../team-data-science-process/lifecycle.md).

Un exemple de structure de projet telle qu’elle apparaît pendant l’exécution ou une fois terminée est illustré dans le panneau gauche de la figure ci-après. Ce projet provient de [l’exemple de projet Team Data Science Process portant sur la classification des revenus à partir de données de recensement aux États-Unis dans Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).

![Exemple de structure de projet](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Documenter votre projet
Pour plus d’informations sur la procédure à suivre pour documenter votre projet, consultez les [modèles de documentation TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Dans le modèle de documentation TDSP Machine Learning actuel, nous vous conseillons d’inclure toutes les informations figurant dans le fichier [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). Ce modèle doit être rempli avec des informations propres à votre projet. 

Nous fournissons également un modèle [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings). Vous pouvez utiliser ce modèle pour inclure des informations qui ne figurent pas dans le document de projet principal, mais qu’il est malgré tout utile de fournir. 

### <a name="example-project-report"></a>Exemple de rapport de projet
Un [exemple de rapport de projet](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md) est mis à votre disposition. Ce rapport concernant [l’exemple de projet de classification des revenus aux États-Unis](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) indique comment instancier et utiliser le modèle TDSP pour un projet de science des données.

## <a name="next-steps"></a>Étapes suivantes
Pour vous aider à comprendre la procédure d’utilisation de la structure et des modèles TDSP dans des projets Machine Learning, nous fournissons plusieurs exemples de projets complets dans la documentation de Machine Learning :

- Pour découvrir un exemple illustrant la création d’un projet TDSP dans Machine Learning, consultez [l’exemple de projet Team Data Science Process portant sur la classification des revenus à partir de données de recensement aux États-Unis dans Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Pour examiner un exemple utilisant l’apprentissage approfondi et le traitement en langage naturel dans un projet instancié en TDSP dans Machine Learning, consultez [l’exemple portant sur la reconnaissance d’entité bio-médicale à l’aide d’un traitement en langage naturel avec apprentissage approfondi](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

