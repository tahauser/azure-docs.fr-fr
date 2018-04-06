---
title: Développement Agile de projets de science des données - Azure Machine Learning | Microsoft Docs
description: Comment les développeurs peuvent réaliser, au sein d’une équipe, un projet de science des données d’une manière systématique, collaborative et avec gestion de versions, à l’aide du processus Team Data Science Process.
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
ms.date: 11/28/2017
ms.author: bradsev
ms.openlocfilehash: dbaf2df0f5572c9b269000c741f1d736a7521d73
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="agile-development-of-data-science-projects"></a>Développement Agile de projets de science des données

Ce document explique comment les développeurs peuvent réaliser, au sein d’une équipe, un projet de science des données d’une manière systématique, collaborative et avec gestion de versions, à l’aide du [processus TDSP](overview.md) (Team Data Science Process). TDSP est un cadre élaboré par Microsoft qui propose une succession structurée d’activités visant à exécuter des solutions d’analytique prédictive basées sur le cloud avec efficacité. Pour obtenir une description des rôles des membres de l’équipe de science des données et des tâches qui leur incombent dans le cadre de ce processus, consultez [Rôles et tâches du processus TDSP](roles-tasks.md). 

Cet article fournit des instructions pour les procédures suivantes : 

1. Utiliser la **planification sprint** pour les éléments du projet.<br> Si vous ne connaissez pas bien la planification sprint, vous pouvez obtenir des informations générales et détaillées [ici](https://en.wikipedia.org/wiki/Sprint_(software_development) "ici"). 
2. **Ajouter des éléments de travail** à une planification sprint. 

> [!NOTE]
> Les étapes nécessaires à la configuration d’un environnement d’équipe TDSP à l’aide de Visual Studio Team Services (VSTS) sont décrites dans les instructions suivantes. Ces instructions indiquent comment accomplir ces tâches avec VSTS, car c’est de cette façon que le processus TDSP est implémenté chez Microsoft.  Les éléments (3) et (4) de la liste précédente font partie des avantages que présente l’utilisation de VSTS. Si une autre plateforme d’hébergement de code est utilisée pour votre groupe, les tâches que doit effectuer le responsable d’équipe ne changent généralement pas. En revanche, c’est la façon dont vont s’effectuer ces tâches qui sera différente. Par exemple, l’élément de la section 6 (**Lier un élément de travail à une branche Git**) peut ne pas être aussi simple qu’avec VSTS.
>
>

La figure suivante montre le flux de travail classique de planification sprint, de code et de contrôle de code source impliqué dans l’implémentation d’un projet de science des données :

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologie 

Dans le framework de planification sprint TDSP, il existe quatre types **d’éléments de travail** fréquemment utilisés : **Caractéristique**, **Récit**, **Tâche** et **Bogue**. Chaque projet d’équipe met à jour un backlog de tous les éléments de travail. Il n’existe pas de backlog au niveau du dépôt Git, sous les projets d’équipe. Voici leurs définitions :

- **Caractéristique** : une caractéristique correspond à un engagement de projet. Les différents engagements pris auprès d’un client sont considérés comme des caractéristiques distinctes. De même, il est préférable de considérer les différentes phases d’un projet client comme des caractéristiques distinctes. Si vous choisissez un format tel que ***NomClient-NomEngagement*** pour nommer vos caractéristiques, vous pourrez facilement connaître le contexte du projet ou de l’engagement grâce au nom qu’il porte.
- **Récit** : les récits sont des éléments de travail qui sont nécessaires pour mener une caractéristique (un projet) de bout en bout. Voici quelques exemples de récits :
    - Obtention de données 
    - Exploration de données 
    - Génération de caractéristiques
    - Création de modèles
    - Utilisation de modèles 
    - Nouvel apprentissage de modèles
- **Tâche** : les tâches sont des éléments de travail de code ou de document pouvant être assignés, ou autres activités devant être effectuées pour mener à bien un récit. Par exemple, les tâches du récit *Obtention de données* pourraient être les suivantes :
    -  Obtention d’informations d’identification SQL Server 
    -  Chargement de données dans SQL Data Warehouse 
- **Bogue** : les bogues désignent généralement les corrections qui doivent être apportées à du code ou à un document existant au moment de terminer une tâche. Si le bogue est dû à l’absence d’étapes ou de tâches, il peut passer au statut de récit ou de tâche, selon le cas. 

> [!NOTE]
> Les concepts de caractéristiques, de récits, de tâches et de bogues ont été empruntés à la gestion de code logiciel qui est utilisée en science des données. Leurs définitions peuvent varier légèrement de celles données par la gestion de code logiciel.
>
>

> [!NOTE]
> Les scientifiques des données se sentiront peut-être plus à l’aise avec un modèle agile, qui s’aligne spécifiquement sur les étapes du cycle de vie TDSP. Dans cette optique, un modèle de planification sprint basé sur l’approche Agile a été créé, où les épopées, récits, etc. sont remplacés par les étapes ou sous-étapes du cycle de vie TDSP. Pour obtenir des instructions sur la création d’un modèle agile, consultez [Configurer le processus de science des données agile dans Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Planification sprint 

La planification sprint est utile pour la définition des priorités d’un projet, ainsi que pour la planification et l’allocation des ressources. De nombreux scientifiques des données sont engagés dans plusieurs projets à la fois, qui peuvent durer des mois. Les projets évoluent souvent à des rythmes différents. Sur le serveur VSTS, vous pouvez facilement créer, gérer et effectuer le suivi des éléments de travail de votre projet d’équipe, ainsi qu’effectuer une planification sprint en vue de garantir le bon déroulement du projet. 

Pour obtenir des instructions relatives à la planification sprint dans VSTS, cliquez sur [ce lien](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning). 


## 3. <a name='AddFeature-3'></a>Ajouter une caractéristique  

Une fois que vous avez créé le dépôt de votre projet sous un projet d’équipe, accédez à la page **Vue d’ensemble** de l’équipe, puis cliquez sur **Gérer le travail**.

![2](./media/agile-development/2-sprint-team-overview.png)

Pour inclure une caractéristique dans le backlog, cliquez sur **Backlogs** --> **Caractéristiques** --> **Nouveau**, tapez le **Titre** de la caractéristique (il s’agit généralement du nom de votre projet), puis cliquez sur **Ajouter**.

![3](./media/agile-development/3-sprint-team-add-work.png)

Double-cliquez sur la caractéristique que vous avez créée. Ajoutez les descriptions, affectez les membres de l’équipe à la caractéristique, puis définissez les paramètres de planification. 

Vous pouvez également lier cette caractéristique au dépôt du projet. Sous la section **Développement**, cliquez sur **Ajouter un lien**. Une fois les modifications apportées à la caractéristique, cliquez sur **Enregistrer et fermer**.


## 4. <a name='AddStoryunderfeature-4'></a>Ajouter un récit sous une caractéristique 

Les récits peuvent être ajoutés sous une caractéristique pour décrire les principales étapes nécessaires pour achever le projet (ou caractéristique). Pour ajouter un nouveau récit, cliquez sur le symbole **+** situé à gauche de la caractéristique, dans la vue Backlog.  

![4](./media/agile-development/4-sprint-add-story.png)

Vous pouvez modifier les détails du récit, tels que son état, sa description, ses commentaires, sa planification et sa priorité, dans la fenêtre contextuelle.

![5.](./media/agile-development/5-sprint-edit-story.png)

Vous pouvez lier ce récit à un dépôt existant en cliquant sur **+ Ajouter un lien** sous **Développement**. 

![6.](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Ajouter une tâche à un récit 

Les tâches sont des étapes détaillées qui sont nécessaires à la réalisation d’un récit. Une fois que toutes les tâches d’un récit sont terminées, le récit doit également être terminé. 

Pour ajouter une tâche à un récit, cliquez sur le symbole **+** en regard de l’élément de récit, sélectionnez **Tâche**, puis renseignez les informations détaillées de cette tâche dans la fenêtre contextuelle.

![7](./media/agile-development/7-sprint-add-task.png)

Une fois que vous avez créé les caractéristiques, les récits et les tâches, vous pouvez les afficher dans la vue **Backlog** ou **Tableau** pour connaître leur état.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9.](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a>Configurer un modèle de travail TDSP Agile dans Visual Studio Online

Cet article explique comment configurer un modèle de processus de science des données agile qui utilise les étapes de cycle de vie de science des données TDSP et effectue le suivi des éléments de travail avec Visual Studio Online (vso). Les étapes ci-dessous vous montrent, au moyen d’un exemple, comment configurer le modèle de processus de science des données agile *AgileDataScienceProcess* et créer des éléments de travail de science des données basés sur le modèle.

### <a name="agile-data-science-process-template-setup"></a>Configuration du modèle de processus de science des données agile

1. Accédez à la page d’accueil du serveur, **Configurer** -> **Processus**.

    ![10](./media/agile-development/10-settings.png) 

2. Accédez à **Tous les processus** -> **Processus**, puis sous **Agile**, cliquez sur **Créer un processus hérité**. Ensuite, indiquez « AgileDataScienceProcess » comme nom de processus et cliquez sur **Créer un processus**.

    ![11](./media/agile-development/11-agileds.png)

3. Sous l’onglet **AgileDataScienceProcess** -> **Types d’éléments de travail**, désactivez les types d’élément de travail **Épopée**, **Fonctionnalité**, **Récit utilisateur** et **Tâche** en choisissant **Configurer -> Désactiver**.

    ![12](./media/agile-development/12-disable.png)

4. Accédez à l’onglet **AgileDataScienceProcess** -> **Niveaux de backlog**. Renommez « Épopées » en « TDSP Projects » (Projets TDSP) en cliquant sur **Configurer** -> **Modifier/Renommer**. Dans la même boîte de dialogue, cliquez sur **Nouveau type d’élément de travail** dans « Projet de science des données » et définissez la valeur de **Type d’élément de travail par défaut** sur « TDSP Project » (Projet TDSP). 

    ![13.](./media/agile-development/13-rename.png)  

5. De même, remplacez le nom de backlog « Caractéristiques » par « TDSP Stages » (Étapes TDSP) et ajoutez ce qui suit à **Nouveau type d’élément de travail** :

    - Présentation de l’entreprise
    - Acquisition de données
    - Modélisation
    - Déploiement

6. Renommez « Récit utilisateur » en « TDSP Substages » (Sous-étapes TDSP) avec le type d’élément de travail par défaut défini sur le type « TDSP Substage » (Sous-étape TDSP) récemment créé.

7. Définissez les « Tâches » sur le type d’élément de travail « TDSP Task » (Tâche TDSP) récemment créé. 

8. Une fois ces étapes effectuées, les niveaux de backlog doivent ressembler à ceci :

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Créer des éléments de travail de science des données

Une fois le modèle de processus de science des données créé, vous pouvez créer vos éléments de travail de science des données correspondant au cycle de vie TDSP et effectuer leur suivi.

1. Quand vous créez un projet d’équipe, sélectionnez « Agile\AgileDataScienceProcess » comme **Processus d’élément de travail** :

    ![15](./media/agile-development/15-newproject.png)

2. Accédez au projet d’équipe créé, puis cliquez sur **Travail** -> **Backlogs**.

3. Rendez « TDSP Projects » visible en cliquant sur **Configurer les paramètres de l’équipe**, cochez « TDSP Projects », puis enregistrez.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Vous pouvez maintenant commencer à créer des éléments de travail spécifiques de la science des données.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Les éléments de travail du projet de science des données doivent apparaître comme dans l’exemple ci-dessous :

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Étapes suivantes

[Développement collaboratif avec Git](collaborative-coding-with-git.md) explique comment développer le code de projets de science des données en utilisant Git comme framework de développement de code partagé et comment lier ces activités de code au travail planifié avec le processus agile.

Voici des liens supplémentaires vers des ressources sur les processus agiles.

- Processus Agile   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Types d’éléments de travail et flux de travail du processus Agile   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Des procédures pas à pas illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. L’article [Exemples de procédures pas à pas](walkthroughs.md) les liste et les décrit brièvement, en les accompagnant de liens. Ces procédures illustrent comment combiner des outils et services locaux ou cloud dans un flux de travail ou un pipeline pour créer une application intelligente. 
