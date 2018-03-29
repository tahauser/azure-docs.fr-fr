---
title: Phase de modélisation du cycle de vie du processus TDSP (Team Data Science Process) - Azure | Microsoft Docs
description: Objectifs, tâches et livrables associés à la phase de modélisation de vos projets de science des données
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
ms.openlocfilehash: 8ef76e32e0a78f4ca492e6ac6d5755fdf19e734c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="modeling"></a>Modélisation

Cet article présente les objectifs, tâches et livrables associés à la phase de modélisation du processus TDSP. Ce processus indique un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie expose les principales phases que les projets exécutent généralement, souvent de manière itérative :

   1. **Présentation de l’entreprise**
   2. **Acquisition et compréhension des données**
   3. **Modélisation**
   4. **Déploiement**
   5. **Acceptation du client**

Voici une représentation visuelle du cycle de vie TDSP :

![Cycle de vie TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objectifs
* Déterminez les caractéristiques de données optimales pour le modèle d’apprentissage automatique.
* Créez un modèle d’apprentissage automatique informatif qui prédit la cible avec le plus de précision.
* Créez un modèle d’apprentissage automatique adapté à la production.

## <a name="how-to-do-it"></a>Marche à suivre
Trois tâches principales sont traitées dans cette phase :

  * **Ingénierie des caractéristiques** : créez des caractéristiques de données à partir des données brutes pour faciliter l’apprentissage du modèle.
  * **Apprentissage du modèle** : recherchez le modèle qui répond le plus précisément à la question en comparant leurs mesures de réussite.
  * Déterminez si votre modèle est **approprié pour la production**.

### <a name="feature-engineering"></a>Ingénierie des caractéristiques
L’ingénierie des caractéristiques implique l’inclusion, l’agrégation et la transformation des variables brutes pour créer les caractéristiques utilisées dans l’analyse. Pour savoir ce qui influence un modèle, vous devez comprendre comment les caractéristiques sont liées entre elles et comment les algorithmes d’apprentissage automatique sont susceptibles d’utiliser ces caractéristiques. 

Cette étape nécessite une combinaison inédite d’expertise dans le secteur et d’informations obtenues à partir de l’étape d’exploration de données. L’ingénierie des caractéristiques consiste à trouver un équilibre entre la recherche et l’inclusion des variables informatives tout en essayant d’éviter un nombre trop élevé de variables non liées. Les variables informatives améliorent vos résultats, alors que les variables non liées introduisent des bruits superflus dans le modèle. Vous devez également générer ces caractéristiques pour toutes les nouvelles données obtenues au cours de l’évaluation. La génération de ces caractéristiques ne peut donc dépendre que des données qui sont disponibles au moment de l’évaluation. 

Pour obtenir des conseils techniques sur l’ingénierie des caractéristiques durant l’utilisation des diverses technologies de données Azure, consultez [Feature engineering in the Data Science Process](create-features.md) (Ingénierie des caractéristiques dans le processus de science des données). 

### <a name="model-training"></a>Apprentissage du modèle
Selon le type de question auquel vous essayez de réponse, il existe de nombreux algorithmes de modélisation. Pour plus d’informations sur le choix des algorithmes, consultez [Comment choisir les algorithmes dans Microsoft Azure Machine Learning](../studio/algorithm-choice.md). Même si cet article utilise Azure Machine Learning, l’aide qu’il fournit est utile pour tout projet d’apprentissage automatique. 

Le processus d’apprentissage du modèle comprend les étapes suivantes : 

   * **Fractionner les données d’entrée** de manière aléatoire en vue d’une modélisation dans un jeu de données d’apprentissage et un jeu de données de test.
   * **Créer les modèles** à l’aide du jeu de données d’apprentissage.
   * **Évaluer** l’apprentissage et le jeu de données de test. Utiliser une série d’algorithmes d’apprentissage automatique concurrents, ainsi que les divers paramètres associés (*balayage de paramètres*), destinés à répondre à la question digne d’intérêt avec les données actuelles.
   * **Déterminer la solution « optimale »** pour répondre à la question en comparant les mesures de réussite entre différentes méthodes.

> [!NOTE]
> **Éviter la fuite**: vous pouvez utiliser la fuite de données si vous incluez des données extérieures au jeu de données d’apprentissage, qui permet à un modèle ou à un algorithme d’apprentissage automatique d’effectuer des prédictions anormalement correctes. La nervosité qui gagne les scientifiques de données quand ils obtiennent des résultats prédictifs semblant trop beaux pour être vrais est souvent liée à une fuite. Ces dépendances peuvent être difficiles à détecter. Pour éviter toute fuite, il est souvent nécessaire de jongler entre la création d’un jeu de données d’analyse, la création d’un modèle et l’évaluation de la précision des résultats. 
> 
> 

Nous fournissons avec le processus TDSP un [outil de modélisation et de création de rapports automatisé](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) capable d’exécuter plusieurs algorithmes et balayages de paramètres pour produire un modèle de référence. Il génère également un rapport de modélisation de base qui résume les performances de chaque combinaison de modèle et de paramètre, y compris l’importance des variables. Ce processus est également itératif, car il peut influer sur l’ingénierie des caractéristiques. 

## <a name="artifacts"></a>Artefacts
Les artefacts générés au cours de cette phase sont les suivants :

   * [Jeux de caractéristiques](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets) : les caractéristiques développées pour la modélisation sont décrites dans la section **Feature Sets (Jeux de caractéristiques)** du rapport **Data Definition (Définition des données)**. Elle contient des pointeurs vers le code permettant de générer les caractéristiques et une description de la génération de la caractéristique.
   * [Modèle de rapport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md) : pour chaque modèle tenté, un rapport standard basé sur le modèle fournissant des détails sur chaque expérience est produit.
   * **Décision de point de contrôle** : déterminez si le modèle est suffisamment performant pour être déployé sur un système de production. Voici certaines questions clés que vous devez vous poser :
     * Le modèle répond-il à la question avec une fiabilité suffisante compte tenu des données de test ? 
     * Devez-vous essayer d’appliquer d’autres approches ? Devez-vous collecter des données supplémentaires, poursuivre l’ingénierie des caractéristiques ou faire des essais avec d’autres algorithmes ?

## <a name="next-steps"></a>Étapes suivantes

Voici les liens vers chaque étape du cycle de vie TDSP :

   1. [Présentation de l’entreprise](lifecycle-business-understanding.md)
   2. [Acquisition et compréhension des données](lifecycle-data.md)
   3. [Modélisation](lifecycle-modeling.md)
   4. [Déploiement](lifecycle-deployment.md)
   5. [Acceptation du client](lifecycle-acceptance.md)

Nous indiquons des procédures pas à pas complètes qui illustrent toutes les étapes du processus correspondant à des scénarios spécifiques. L’article [Example walkthroughs](walkthroughs.md) (Exemples de procédures pas à pas) contient une liste des scénarios ainsi que des liens et des descriptions de miniatures. Les procédures pas à pas montrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente. 

Pour obtenir des exemples sur l’exécution de procédures dans les processus TDSP utilisant Azure Machine Learning Studio, consultez [Utilisation du processus de science des données avec Azure Machine Learning](http://aka.ms/datascienceprocess). 
