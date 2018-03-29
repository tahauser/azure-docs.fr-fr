---
title: Phase de présentation de l’entreprise du cycle de vie du processus TDSP (Team Data Science Process) - Azure | Microsoft Docs
description: Objectifs, tâches et livrables associés à la phase de présentation de l’entreprise de vos projets de science des données
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
ms.openlocfilehash: daaaccaeac196b5f4651183173c8e83085d5184d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="business-understanding"></a>Présentation de l’entreprise

Cet article présente les objectifs, tâches et livrables associés à la phase de présentation de l’entreprise du processus TDSP. Ce processus indique un cycle de vie recommandé que vous pouvez utiliser pour structurer vos projets de science des données. Le cycle de vie expose les principales phases que les projets exécutent généralement, souvent de manière itérative :

   1. **Présentation de l’entreprise**
   2. **Acquisition et compréhension des données**
   3. **Modélisation**
   4. **Déploiement**
   5. **Acceptation du client**

Voici une représentation visuelle du cycle de vie TDSP : 

![Cycle de vie TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objectifs
* Spécifier les variables clés devant servir de cibles du modèle et dont les mesures associées permettent de déterminer la réussite du projet.
* Identifier les sources de données pertinentes auxquelles l’entreprise a ou doit avoir accès.

## <a name="how-to-do-it"></a>Marche à suivre
Deux tâches principales sont traitées dans cette phase : 

   * **Définir les objectifs** : collaborez avec votre client et d’autres parties prenantes pour comprendre et identifier les problèmes métier. Formulez des questions qui définissent les objectifs commerciaux que les techniques de science des données peuvent cibler.
   * **Identifier les sources de données** : recherchez les données pertinentes qui vous aident à répondre aux questions qui définissent les objectifs du projet.

### <a name="define-objectives"></a>Définir les objectifs
1. Un objectif central de cette étape consiste à identifier les variables d’activité clés que l’analyse doit prédire. Nous appelons ces variables *cibles du modèle*, et utilisons les mesures qui leur sont associées pour déterminer la réussite du projet. Deux exemples de telles cibles sont les prévisions de ventes ou la probabilité qu’une commande soit frauduleuse.

2. Définissez les objectifs du projet en posant et affinant des questions « précises » qui sont à la fois pertinentes, spécifiques et non équivoques. La science des données est un processus utilisant des noms et des nombres pour répondre à ces questions. Pour plus d’informations sur la manière de poser des questions précises, consultez le blog [How to do data science (Comment pratiquer la science des données)](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/). En règle générale, vous utilisez la science des données ou l’apprentissage automatique pour répondre à cinq types de question :
 
   * Quelle quantité (ou combien) ? (régression)
   * Quelle catégorie ? (classification)
   * Quel groupe ? (clustering)
   * Est-ce étrange ? (détection des anomalies)
   * Quelle est l’option appropriée ? (recommandation)

   Parmi les questions indiquées ci-dessus, déterminez celle que vous posez et comment le fait d’y répondre vous permet d’atteindre vos objectifs professionnels.

3. Définissez l’équipe de projet en spécifiant les rôles et responsabilités de ses membres. Développez un plan à étapes général auquel vous vous référez à mesure que vous découvrez de nouvelles informations. 

4. Définissez les mesures de réussite. Par exemple, vous pouvez souhaiter faire une prédiction de la désinscription des clients. Vous avez besoin d’un taux de précision de « x » pour cent d’ici la fin de ce projet de trois mois. Avec ces données, vous pouvez proposer des promotions client pour réduire la désinscription des clients. Les mesures doivent satisfaire à l’acronyme **SMART** : 

   * **S**pécifiques 
   * **M**esurables
   * **A**tteignables 
   * **R**elevant (pertinentes) 
   * **T**ime-bound (associées à un délai) 

### <a name="identify-data-sources"></a>Identifier les sources de données
Identifiez les sources de données qui contiennent des exemples connus de réponses à vos questions précises. Recherchez les données suivantes :

* Données pertinentes pour la question. Disposez-vous de mesures de la cible et de caractéristiques qui sont associées à la cible ?
* Données qui constituent une mesure exacte de votre cible de modèle et des caractéristiques dignes d’intérêt.

Par exemple, vous trouverez peut-être que les systèmes existants doivent collecter et consigner d’autres types de données pour résoudre le problème et atteindre les objectifs du projet. Dans cette situation, vous pouvez rechercher des sources de données externes ou mettre à jour vos systèmes pour collecter des données récentes.

## <a name="artifacts"></a>Artefacts
Voici les livrables de cette phase :

   * [Charte](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md) : un modèle standard est fourni dans la définition de la structure de projet TDSP. La charte est un document interactif. Vous mettez à jour le modèle dans le projet à mesure que vous effectuez de nouvelles détections et lorsque les besoins métier changent. Il est important que vous vous référiez à ce document, en ajoutant des détails, à mesure que vous progressez dans le processus de découverte. Veillez à ce que les clients et autres parties prenantes demeurent impliqués dans la réalisation des modifications et communiquez-leur clairement les raisons des modifications.  
   * [Sources de données](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources) : la section **Raw data sources (Sources de données brutes)** du rapport **Data definitions (Définitions des données)** qui figure dans le dossier **Data report (Rapport de données)** du projet TDSP contient les sources de données. Cette section spécifie les emplacements d’origine et de destination des données brutes. Au cours des phases ultérieures, vous renseignez des détails supplémentaires, tels que les scripts permettant de déplacer les données vers votre environnement d’analyse.  
   * [Dictionnaires de données](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries) : ce document fournit les descriptions des données fournies par le client. Ces descriptions incluent des informations sur le schéma (types de données et informations sur les règles de validation, le cas échéant) et les diagrammes entité-relation s’ils sont disponibles.

## <a name="next-steps"></a>Étapes suivantes

Voici les liens vers chaque étape du cycle de vie TDSP :

   1. [Présentation de l’entreprise](lifecycle-business-understanding.md)
   2. [Acquisition et compréhension des données](lifecycle-data.md)
   3. [Modélisation](lifecycle-modeling.md)
   4. [Déploiement](lifecycle-deployment.md)
   5. [Acceptation du client](lifecycle-acceptance.md)

Nous indiquons des procédures pas à pas complètes qui illustrent toutes les étapes du processus correspondant à des scénarios spécifiques. L’article [Example walkthroughs](walkthroughs.md) (Exemples de procédures pas à pas) contient une liste des scénarios ainsi que des liens et des descriptions de miniatures. Les procédures pas à pas montrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente. 

Pour obtenir des exemples sur l’exécution de procédures dans les processus TDSP utilisant Azure Machine Learning Studio, consultez [Utilisation du processus de science des données avec Azure Machine Learning](http://aka.ms/datascienceprocess).
