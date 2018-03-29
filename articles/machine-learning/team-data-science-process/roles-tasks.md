---
title: Rôles et tâches du responsable de l’équipe Team Data Science Process - Azure | Microsoft Docs
description: Présentation des composants clés, rôles du personnel et tâches associées pour un projet d’équipe de science des données.
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
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: b92bb8e633aa859324f2ff46f5d76a5934007ccf
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="team-data-science-process-roles-and-tasks"></a>Rôles et tâches du responsable de l’équipe Team Data Science Process

Team Data Science Process (TDSP) est une infrastructure développée par Microsoft qui fournit une méthodologie structurée pour créer efficacement des solutions d’analyse prédictive et des applications intelligentes. Cet article présente les rôles du personnel clé et les tâches qui incombent à une équipe de science des données dans le cadre de ce processus. 

Cette introduction référence des didacticiels qui fournissent des instructions sur la façon de configurer l’environnement TDSP pour tout le groupe de science des données, les équipes de science des données et les projets. Nous fournissons des conseils détaillés en utilisant Visual Studio Team Services (VSTS) dans les didacticiels comme notre plateforme d’hébergement de code et outil de planification agile permettant de gérer les tâches de l’équipe, de contrôler l’accès et de gérer les référentiels. 

Vous pourrez également utiliser ces informations pour implémenter TDSP dans votre propre outil d’hébergement de code et de planification agile. 

## <a name="structures-of-data-science-groups-and-teams"></a>Structures des groupes et équipes de science des données
Les fonctions de science des données dans les entreprises sont souvent organisées dans la hiérarchie suivante :

1. ***Groupe/s de sciences des données***

2. ***Équipe/s de sciences des données/s dans le/s groupe/s***

Ce type de structure nécessite des responsables de groupe et d’équipe. En règle générale, un projet de science des données est effectué par une équipe de science des données, qui peut être composée de responsables de projet (pour les tâches de gestion de projet et de gouvernance), de scientifiques des données ou d’ingénieurs (contributeurs individuels/personnel technique) qui s’occuperont des phases du projet consacrées à la science des données et à l’ingénierie. Avant l’exécution, la configuration et la gouvernance sont effectuées par les responsables de groupe, d’équipe ou de projet.

## <a name="definition-of-four-tdsp-roles"></a>Définition des quatre rôles TDSP
Dans l’hypothèse ci-dessus, nous avons spécifié quatre rôles distincts pour le personnel de l’équipe :

1. ***Responsable de groupe***. Le responsable de groupe est le responsable de l’ensemble de l’unité de science des données dans une entreprise. Une unité de science des données peut avoir plusieurs équipes, chacune travaillant sur plusieurs projets de science des données dans différents secteurs d’activité. Un responsable de groupe peut déléguer ses tâches à un remplaçant, mais les tâches associées au rôle ne changent pas.

2. ***Responsable d’équipe***. Un responsable d’équipe gère l’équipe Science des données d’une entreprise. Cette équipe est constituée de plusieurs scientifiques des données. Si le service chargé de la science des données ne comprend que quelques scientifiques des données, une même personne peut jouer le rôle de Responsable de groupe et de Responsable d’équipe.

3. ***Coordinateur de projet***. Un coordinateur de projet gère les activités quotidiennes des différents scientifiques des données dans le cadre d’un projet de science des données spécifique.

4. ***Contributeur individuel de projet***. Scientifique des données, analyste d’entreprise, ingénieur, architecte, etc. Un contributeur individuel de projet exécute un projet de science des données. 


**[AZURE.NOTE]** : selon la structure d’une entreprise, une même personne peut avoir plusieurs rôles, ou plusieurs personnes peuvent occuper un rôle. C’est souvent le cas dans les petites structures ou les entreprises dans lesquelles un petit nombre de personnes s’occupent de la science des données.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Tâches à effectuer par quatre personnes

L’image suivante montre les tâches de niveau supérieur que doit effectuer le personnel par rôle dans le cadre de l’adoption et de l’implémentation du projet Team Data Science Process conceptualisé par Microsoft. 

![Vue d’ensemble des rôles et tâches](./media/roles-tasks/overview-tdsp-top-level.png)

Ce schéma et la présentation suivante, plus détaillée, des tâches attribuées à chaque rôle dans le projet TDSP doivent vous aider à choisir le didacticiel approprié en fonction de vos responsabilités au sein de l’organisation.

>[AZURE.NOTE] Dans les instructions suivantes, nous montrons comment configurer un environnement TDSP et effectuer d’autres tâches de science des données dans Visual Studio Team Services (VSTS). Nous indiquons comment accomplir ces tâches avec VSTS, car c’est de cette façon que nous implémentons le processus TDSP chez Microsoft. VSTS facilite la collaboration en intégrant la gestion des éléments qui effectuent le suivi des tâches et un service d’hébergement de code utilisé pour partager les utilitaires, organiser les versions et garantir une sécurité basée sur les rôles. Vous pouvez choisir d’autres plateformes pour implémenter les tâches présentées par le projet TDSP. Mais, selon votre plateforme, certaines fonctionnalités issues de VSTS peuvent ne pas être disponibles. 
>
>Nous utilisons également la [Machine virtuelle de science des données (DSVM)](http://aka.ms/dsvm) sur le cloud Azure comme bureau d’analyse avec plusieurs outils de science des données courants, préconfigurés et intégrés avec divers logiciels Microsoft et services Azure. Vous pouvez utiliser la machine DSVM ou tout autre environnement de développement pour implémenter TDSP. 


## <a name="group-manager-tasks"></a>Tâches du responsable de groupe

Les tâches suivantes sont effectuées par le Responsable de groupe (ou un administrateur du système TDSP désigné) afin d’adopter le projet TDSP :

- Créer un **compte de groupe** sur une plateforme d’hébergement de code (par exemple, Github, Git, VSTS, entre autres)
- Créer un **référentiel de modèle de projet** sur le compte de groupe et l’alimenter à l’aide du référentiel de modèle de projet développé par l’équipe TDSP de Microsoft. Le référentiel du modèle de projet TDSP de Microsoft offre une **structure de répertoires normalisée** incluant des répertoires pour les données, le code et les documents, et fournit un ensemble de **modèles de documents normalisés** pour optimiser le processus de science des données. 
- Créer un **référentiel d’utilitaire** et l’alimenter à l’aide du référentiel d’utilitaire développé par l’équipe TDSP de Microsoft. Le référentiel d’utilitaire TDSP de Microsoft fournit un ensemble d’utilitaires qui optimisent le travail du spécialiste des données, y compris des utilitaires pour l’exploration interactive des données, l’analyse, la création de rapports, et la modélisation et la création de rapports de référence.
- Configurer la **stratégie de contrôle de sécurité** de ces deux référentiels sur votre compte de groupe.  

Pour obtenir des instructions détaillées, consultez [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Tâches du responsable d’équipe

Les tâches suivantes sont effectuées par le Responsable d’équipe (ou un administrateur de projet d’équipe désigné) afin d’adopter le projet TDSP :

- Si VSTS est sélectionné comme plateforme d’hébergement de code pour le contrôle de version et la collaboration, créez un **projet d’équipe** sur le serveur VSTS du groupe. Sinon, cette tâche peut être ignorée.
- Créer le **référentiel de modèles du projet d’équipe** sous le projet d’équipe, puis l’alimenter à l’aide du référentiel de modèle de projet de groupe configuré par votre responsable de groupe ou le délégué du responsable. 
- Créer le **référentiel d’utilitaires d’équipe** et ajouter les utilitaires spécifiques à l’équipe au référentiel. 
- (Facultatif) Créer un **[stockage de fichiers Azure](https://azure.microsoft.com/services/storage/files/)** à utiliser pour stocker les ressources de données qui peuvent être utiles à toute l’équipe. D’autres membres de l’équipe peuvent monter ce magasin de fichiers partagés cloud sur leur bureau d’analyse.
- (Facultatif) Monter le stockage de fichiers Azure sur l’instance **DSVM** (Data Science Virtual Machine) du responsable d’équipe, et y ajouter des ressources de données.
- Configurer le **contrôle de sécurité** en ajoutant des membres d’équipe et en configurant leurs privilèges. 

Pour obtenir des instructions détaillées, consultez [Tâches du responsable d’équipe pour une équipe de science des données](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Tâches du coordinateur de projet

Les tâches suivantes sont effectuées par le responsable de projet pour adopter le projet TDSP :

- Créer un **référentiel de projet** sous le projet d’équipe et l’alimenter à l’aide du référentiel du modèle de projet d’équipe. 
- (Facultatif) Créer un **stockage de fichiers Azure** à utiliser pour stocker les ressources de données du projet. 
- (Facultatif) Monter le stockage de fichiers Azure sur l’instance **DSVM** (Data Science Virtual Machine) du responsable de projet, et y ajouter des ressources de données de projet.
- Configurer le **contrôle de sécurité** en ajoutant des membres de projet et en configurant leurs privilèges. 

Pour obtenir des instructions détaillées, consultez [Tâches du responsable de projet pour une équipe de science des données](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Tâches du contributeur individuel de projet

Les tâches suivantes sont effectuées par un contributeur individuel de projet (habituellement un scientifique des données) afin de mener le projet de science des données à l’aide de TDSP :

- Cloner le **référentiel de projet** configuré par le responsable de projet. 
- (Facultatif) Monter le **stockage de fichiers Azure** partagé de l’équipe et du projet sur leur **Machine virtuelle de science des données** (DSVM).
- Exécuter le projet. 

 
Pour obtenir des instructions détaillées sur l’intégration dans un projet, consultez [Contributeurs individuels de projet pour une équipe de science des données](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Exécution du projet de science des données
 
En suivant l’ensemble d’instructions approprié, les scientifiques des données, le responsable de projet et les responsables d’équipe peuvent créer des éléments de travail afin d’effectuer le suivi de toutes les tâches et étapes nécessaires du début à la fin d’un projet. L’utilisation de Git favorise également la collaboration entre les scientifiques de données et garantit que les artefacts générés pendant l’exécution du projet sont contrôlés au niveau de la version et partagés par tous les membres du projet.

Les instructions fournies pour l’exécution du projet ont été développées en partant du principe que les deux éléments de travail et les référentiels Git du projet se trouvent sur VSTS. Utiliser VSTS pour ces composants vous permet de lier vos éléments de travail avec les branches Git des référentiels de votre projet. De cette façon, vous pouvez facilement suivre ce qui a été effectué au niveau d’un élément de travail. 

La figure suivante présente ce flux de travail pour l’exécution du projet à l’aide de TDSP.

![Exécution d’un projet de science des données standard](./media/roles-tasks/overview-project-execute.png)

Le flux de travail inclut des étapes qui peuvent être regroupées en trois activités :

- Planification sprint (coordinateur de projet)
- Développement des artefacts sur les branches Git pour traiter les éléments de travail (scientifique des données)
- Révision du code et fusion des branches avec les branches principales (coordinateur de projet ou autres membres de l’équipe)

Pour obtenir des instructions détaillées sur le flux de travail d’exécution du projet, consultez [Réalisation de projets science des données](project-execution.md).

## <a name="next-steps"></a>Étapes suivantes

Les liens ci-dessous donnent accès à des descriptions plus détaillées des rôles et des tâches définis par le processus TDSP (Team Data Science Process) :

- [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md)
- [Tâches du responsable d’une équipe de science des données](team-lead-tasks.md)
- [Tâches du coordinateur de projet pour une équipe de science des données](project-lead-tasks.md)
- [Contributeurs individuels de projet pour une équipe de science des données](project-ic-tasks.md)