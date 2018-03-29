---
title: Qu’est-ce que le processus Team Data Science Process ? | Microsoft Docs
description: Propose une méthodologie de science des données permettant de fournir des solutions d’analyse prédictive et des applications intelligentes.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: bradsev
ms.openlocfilehash: f7c081dcd74164f4b1f054f5a65f2ff6aaabebd7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-the-team-data-science-process"></a>Qu’est-ce que le processus Team Data Science Process ?

Le processus TDSP (Team Data Science Process) est une méthodologie de science des données agile et itérative permettant de fournir des solutions d’analyse prédictive et des applications intelligentes avec efficacité. Le processus TDSP permet d’améliorer la collaboration et l’apprentissage au sein des équipes. Il distille les meilleures pratiques et structures de Microsoft et d’autres acteurs du secteur qui facilitent l’implémentation d’initiatives de science des données. L’objectif est d’aider les entreprises à exploiter pleinement les avantages de leur programme d’analytique.

Cet article fournit une vue d’ensemble du processus TDSP et de ses principaux éléments. Nous proposons ici une description générique du processus, qui peut être implémentée avec un large éventail d’outils. Une description plus détaillée des tâches du projet et des rôles impliqués dans le cycle de vie du processus est accessible dans les liens vers des rubriques complémentaires. Cet article fournit également une aide à l’implémentation du processus TDSP grâce à un ensemble spécifique d’outils et d’infrastructure Microsoft, que nous utilisons dans nos équipes.

## <a name="key-components-of-the-tdsp"></a>Composants clés du processus TDSP

Le processus TDSP est constitué des composants clés suivants :

- une définition du **cycle de vie de la science des données** ;
- une **structure de projet normalisée** ;
- une **Infrastructure et des ressources** pour les projets de science des données ;
- des **outils et utilitaires** pour mener à bien le projet.


## <a name="data-science-lifecycle"></a>Cycle de vie de la science des données

Le processus TDSP (Team Data Science Process) fournit un cycle de vie qui structure le développement des projets de sciences des données. Le cycle de vie présente les étapes, du début à la fin, que les projets suivent généralement quand ils sont exécutés.

Si vous utilisez un autre cycle de vie de sciences des données, tel que [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) ou le processus personnalisé de votre organisation, vous pouvez toujours utiliser le processus TDSP basé sur les tâches dans le contexte de ces cycles de vie de développement. À haut niveau, ces méthodologies ont beaucoup en commun. 

Ce cycle de vie a été conçu pour les projets de sciences des données intégrés à des applications intelligentes. Ces applications déploient des modèles d’apprentissage automatique ou d’intelligence artificielle pour l’analytique prédictive. Les projets de sciences des données exploratoires et les projets d’analytique ad hoc peuvent également tirer parti de ce processus. Mais, dans ce cas, certaines des étapes décrites peuvent s’avérer superflues.    

Le cycle de vie expose les principales phases que les projets exécutent généralement, souvent de manière itérative :

* **Présentation de l’entreprise**
* **Acquisition de données et compréhension**
* **Modélisation**
* **Déploiement**
* **Acceptation du client**

Voici une représentation visuelle du **cycle de vie du processus TDSP (Team Data Science Process)**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

Les objectifs, tâches et artefacts de documentation de chaque phase du cycle de vie TDSP sont décrits dans la rubrique [Cycle de vie du processus TDSP (Team Data Science Process)](lifecycle.md). Ces tâches et artefacts sont associés à des rôles de projet :

- Architecte de solution
- Chef de projet
- Scientifique des données
- Coordinateur de projet 

Le diagramme suivant présente la grille des tâches (en bleu) et des artefacts (en vert) associés à chaque étape du cycle de vie (sur l’axe horizontal) pour ces rôles (sur l’axe vertical). 

![rôles-et-tâches-du-processus-TDSP](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Structure de projet normalisée

Lorsque tous les projets partagent une structure de répertoire et utilisent des modèles de documents de projet, il est plus facile pour les collaborateurs de l’équipe de rechercher des informations sur les projets. Tout le code et tous les documents sont stockés dans un système de contrôle de version (CVS) comme Git, TFS ou Subversion pour permettre la collaboration au sein de l’équipe. Le suivi des tâches et des fonctionnalités dans un système de suivi de projet agile tel que Jira, Rally ou Visual Studio Team Services permet de suivre de plus près chacun des composants du code. Il permet également aux équipes d’obtenir de meilleurs devis estimatifs. Le processus TDSP recommande la création d’un référentiel distinct pour chaque projet sur le système CVS à des fins de gestion de versions, de sécurité des informations et de collaboration. La structure standardisée de tous les projets permet de centraliser des connaissances institutionnelles à travers l’organisation.

Nous fournissons des modèles de structure de dossiers et des documents requis dans les emplacements standards. Cette structure de dossiers organise les fichiers qui contiennent du code à des fins d’exploration des données et d’extraction de fonctionnalités, et qui enregistrent les itérations du modèle. Ces modèles permettent aux membres de l’équipe de comprendre plus facilement le travail effectué par d’autres et d’ajouter de nouveaux membres aux équipes. Il est aisé d’afficher et de mettre à jour les modèles de documents au format Markdown. Utilisez les modèles pour fournir des listes de vérification contenant des questions clés pour chaque projet et ainsi vous assurer que le problème est bien défini et que les livrables répondent aux attentes de qualité. Voici quelques exemples :

- une charte de projet pour documenter le problème de l’entreprise et le périmètre du projet ;
- des rapports de données pour documenter la structure et les statistiques des données brutes ;
- des modèles de rapports pour documenter les fonctionnalités dérivées ;
- des modèles de métriques de performances, par exemple les courbes ROC ou MSE.


![Répertoires TDSP](./media/overview/tdsp-dir-structure.png)

La structure de répertoires peut être clonée à partir de [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastructure et ressources pour les projets de science des données  

Le processus TDSP fournit des recommandations sur la gestion de l’infrastructure partagée d’analytique et de stockage, notamment :

- systèmes de fichiers cloud pour le stockage des jeux de données ; 
- bases de données
- clusters Big Data (Hadoop ou Spark) ; 
- services de Machine Learning. 

L’infrastructure de stockage et d’analytique peut se trouver sur le cloud ou sur site. C’est à cet endroit que sont stockés les jeux de données bruts et traités. Cette infrastructure permet de reproduire les analyses. Elle évite également la duplication, susceptible de provoquer des incohérences et d’entraîner des coûts d’infrastructure inutiles. Des outils sont fournis pour configurer les ressources partagées, les suivre et permettre à chacun des membres de l’équipe de se connecter à ces ressources en toute sécurité. Il est également recommandé que les participants au projet créent un environnement de calcul homogène. Différents membres de l’équipe pourront alors répliquer et valider les expériences.

Voici l’exemple d’une équipe qui travaille sur plusieurs projets et partage différents composants de l’infrastructure d’analytique cloud.

![Infrastructure TDSP](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Outils et utilitaires pour mener à bien le projet

Dans la plupart des organisations, il est difficile d’introduire des processus. Les outils fournis pour implémenter le processus et le cycle de vie de science des données aident à réduire les obstacles pour améliorer l’uniformité de leur adoption. Le processus TDSP offre un ensemble initial d’outils et de scripts permettant d’accélérer son adoption au sein d’une équipe. Il automatise également certaines tâches courantes du cycle de vie de science des données, notamment l’exploration de données et la modélisation de la ligne de base. Une structure bien définie est fournie pour que chacun puisse ajouter des outils et des utilitaires partagés dans le référentiel de code partagé de leur équipe. Ces ressources peuvent être utilisées par d’autres projets de l’équipe ou de l’organisation. Il est également prévu que le processus TDSP permette de les proposer à l’ensemble de la communauté. Les utilitaires TDSP peuvent être clonés sur [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Étapes suivantes

La rubrique [Processus TDSP (Team Data Science Process) : rôles et tâches](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) présente les rôles clés du personnel et les tâches associées pour une équipe de science des données qui se standardise sur ce processus. 
