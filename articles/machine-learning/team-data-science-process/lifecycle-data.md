---
title: Phase d’acquisition de données et de compréhension du cycle de vie du processus TDSP (Team Data Science Process) - Azure | Microsoft Docs
description: Objectifs, tâches et livrables associés à la phase d’acquisition des données et de compréhension de vos projets de science des données
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 028992561b1c2481dc72af3c4edcc16f65f25105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="data-acquisition-and-understanding"></a>Acquisition de données et compréhension

Cet article présente les objectifs, tâches et livrables associés à la phase d’acquisition de données et de compréhension du processus TDSP. Ce processus indique un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie expose les principales phases que les projets exécutent généralement, souvent de manière itérative :

   1. **Présentation de l’entreprise**
   2. **Acquisition et compréhension des données**
   3. **Modélisation**
   4. **Déploiement**
   5. **Acceptation du client**

Voici une représentation visuelle du cycle de vie TDSP : 

![Cycle de vie TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objectifs
* Générer un jeu de données haute qualité et propre dont la relation avec les variables cibles est comprise. Localiser le jeu de données dans l’environnement d’analyse approprié afin que vous soyez prêt à procéder à la modélisation.
* Développer une architecture de la solution du pipeline de données qui actualise et évalue les données régulièrement.

## <a name="how-to-do-it"></a>Marche à suivre
Trois tâches principales sont traitées dans cette phase :

   * **Ingérer les données** dans l’environnement d’analyse cible.
   * **Explorer les données** pour déterminer si la qualité des données est suffisante pour répondre à la question. 
   * **Configurer un pipeline de données** pour évaluer les données nouvelles ou régulièrement actualisées.

### <a name="ingest-the-data"></a>Ingérer les données
Configurez le processus permettant de déplacer les données des emplacements sources vers les emplacements cibles où vous devez exécuter les opérations d’analyse telles que l’apprentissage et les prédictions. Pour connaître les détails techniques et les options relatifs au déplacement des données avec plusieurs services de données Azure, consultez [Charger des données dans des environnements de stockage à des fins d’analyse](ingest-data.md). 

### <a name="explore-the-data"></a>Exploration des données
Avant de former vos modèles, vous devez développer une parfaite compréhension des données. Les jeux de données réels sont souvent parasités, ne contiennent pas certaines valeurs ou comportent une multitude d’autres anomalies. Vous pouvez utiliser la visualisation et la synthèse des données pour auditer la qualité de vos données et fournir les informations nécessaires pour traiter les données en vue de leur modélisation. Ce processus est souvent itératif.

Le processus TDSP fournit un utilitaire automatisé appelé [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), qui aide à visualiser les données et à préparer des rapports de synthèse de données. Nous vous recommandons de commencer par IDEAR pour explorer les données afin de développer une compréhension initiale des données de manière interactive sans codage. Vous pouvez ensuite écrire du code personnalisé pour l’exploration et la visualisation des données. Pour obtenir de l’aide sur le nettoyage des données, consultez [Tâches de préparation des données pour l’apprentissage automatique amélioré](prepare-data.md).  

Une fois que vous êtes satisfait de la qualité des données nettoyées, l’étape suivante consiste à mieux comprendre les modèles inhérents aux données. Cela vous permet de choisir et de développer un modèle prédictif approprié pour votre cible. Recherchez la preuve indiquant à quel point les données sont bien appropriées à la cible. Déterminez ensuite s’il existe des données suffisantes pour passer aux étapes de modélisation suivantes. Là encore, ce processus est souvent itératif. Vous devrez peut-être rechercher de nouvelles sources de données avec des données plus précises ou mieux appropriées pour compléter le jeu de données identifié au cours de la phase précédente. 

### <a name="set-up-a-data-pipeline"></a>Configurer un pipeline de données
Outre l’ingestion et le nettoyage initiaux des données, vous devez généralement définir un processus pour noter les nouvelles données ou actualiser les données régulièrement dans le cadre d’un processus de formation continue. Pour ce faire, configurez un pipeline de données ou un flux de travail. L’article [Déplacement de données à partir d’un serveur SQL local vers SQL Azure avec Azure Data Factory](move-sql-azure-adf.md) contient un exemple de configuration d’un pipeline avec [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Dans cette étape, vous développez une architecture de la solution du pipeline de données. Vous développez le pipeline parallèlement à l’étape suivante du projet de science des données. Suivant les besoins de votre entreprise et les contraintes de vos systèmes existants auxquels cette solution est en cours d’intégration, le pipeline peut être de type : 

   * Par lot
   * En temps réel ou diffusion en continu 
   * Hybride 

## <a name="artifacts"></a>Artefacts
Voici les livrables de cette phase :

   * [Rapport de qualité des données](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md) : ce rapport contient des synthèses de données, les relations entre chaque attribut et cible, le classement des variables, etc. L’outil [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) fourni dans le cadre du processus TDSP permet de générer rapidement ce rapport sur n’importe quel jeu de données tabulaire tel qu’un fichier CSV ou une table relationnelle. 
   * **Architecture de la solution** : il peut s’agir d’un diagramme ou d’une description de votre pipeline de données que vous utilisez pour exécuter une évaluation ou des prédictions sur de nouvelles données une fois que vous avez créé un modèle. Elle contient également le pipeline avec lequel former le modèle en fonction des nouvelles données. Stockez le document dans le répertoire [Project](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quand vous utilisez le modèle de structure de répertoire TDSP.
   * **Décision de point de contrôle** : avant de procéder à l’ingénierie des caractéristiques et à la modélisation complètes, vous pouvez réévaluer le projet afin de déterminer si cela vaut la peine de le poursuivre. Vous pouvez, par exemple, être en mesure de continuer, avoir besoin de collecter des données supplémentaires ou abandonner le projet s’il n’existe pas de données permettant de répondre à la question.

## <a name="next-steps"></a>Étapes suivantes

Voici les liens vers chaque étape du cycle de vie TDSP :

   1. [Présentation de l’entreprise](lifecycle-business-understanding.md)
   2. [Acquisition et compréhension des données](lifecycle-data.md)
   3. [Modélisation](lifecycle-modeling.md)
   4. [Déploiement](lifecycle-deployment.md)
   5. [Acceptation du client](lifecycle-acceptance.md)

Nous indiquons des procédures pas à pas complètes qui illustrent toutes les étapes du processus correspondant à des scénarios spécifiques. L’article [Example walkthroughs](walkthroughs.md) (Exemples de procédures pas à pas) contient une liste des scénarios ainsi que des liens et des descriptions de miniatures. Les procédures pas à pas montrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente. 

Pour obtenir des exemples sur l’exécution de procédures dans les processus TDSP utilisant Azure Machine Learning Studio, consultez [Utilisation du processus de science des données avec Azure Machine Learning](http://aka.ms/datascienceprocess).
