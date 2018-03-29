---
title: Tâches TDSP (Team Data Science Process) pour un contributeur individuel - Azure | Microsoft Docs
description: Vue d’ensemble des tâches d’un contributeur individuel sur un projet de science des données en équipe.
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
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: af60b188fe8db278297e1ece400bf4398e36094a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="individual-contributor-tasks"></a>Tâches du contributeur individuel

Cette rubrique dresse la liste des tâches dont un contributeur individuel doit s’acquitter pour son équipe de science des données. L’objectif est d’établir un environnement d’équipe collaborative standard conforme à [TDSP](overview.md) (Team Data Science Process). Pour obtenir une description des rôles des membres de l’équipe de science des données et des tâches qui leur incombent dans le cadre de ce processus, consultez [Rôles et tâches du processus TDSP](roles-tasks.md).

Les tâches des contributeurs individuels de projets (scientifiques de données) liées à la configuration de l’environnement TDSP du projet sont décrites comme suit : 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** représente le dépôt que votre groupe gère afin de partager les utilitaires nécessaires sur l’ensemble du groupe. 
- **TeamUtilities** désigne le dépôt que votre équipe gère plus particulièrement pour votre équipe. 

Pour obtenir des instructions sur la façon d’exécuter un projet de science des données sous TDSP, consultez [Exécution de projets de science des données](project-execution.md). 

>[AZURE.NOTE] À travers les instructions suivantes, nous décrivons les étapes nécessaires à la configuration d’un environnement d’équipe TDSP à l’aide de Visual Studio Team Services (VSTS). Nous indiquons comment accomplir ces tâches avec VSTS, car c’est de cette façon que nous implémentons le processus TDSP chez Microsoft. Si une autre plateforme d’hébergement de code est utilisée pour votre groupe, les tâches que doit effectuer le responsable d’équipe ne changent généralement pas. En revanche, c’est la façon dont vont s’effectuer ces tâches qui sera différente.


## <a name="repositories-and-directories"></a>Dépôts et répertoires

Dans ce didacticiel, les noms des dépôts et des répertoires sont abrégés. Cela facilite le suivi des opérations entre les dépôts et les répertoires. Cette notation (**R** pour les dépôts Git et **D** pour les répertoires locaux sur votre instance DSVM) est employée dans les sections suivantes :

- **R2** : le dépôt GroupUtilities sur Git que votre responsable de groupe a configuré sur votre serveur de groupe VSTS.
- **R4** : le dépôt TeamUtilities sur Git que votre responsable d’équipe a configuré.
- **R5** : le dépôt du projet sur Git qui a été configuré par votre coordinateur de projet.
- **D2** : le répertoire local cloné à partir de R2.
- **D4** : le répertoire local cloné à partir de R4.
- **D5** : le répertoire local cloné à partir de R5.


## <a name="step-0-prerequisites"></a>Étape 0 : Prérequis

Pour satisfaire aux prérequis, les tâches attribuées à votre responsable de groupe et décrites dans [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md) doivent être effectuées. En résumé, avant de commencer les tâches du responsable d’équipe, les conditions suivantes doivent être remplies : 
- Votre responsable de groupe a configuré le dépôt **GroupUtilities** (le cas échéant). 
- Votre responsable d’équipe a configuré le dépôt **TeamUtilities** (le cas échéant).
- Votre coordinateur de projet a configuré le dépôt du projet. 
- Vous avez été ajouté à votre dépôt de projet par votre coordinateur de projet, avec le privilège de cloner à partir de ce dépôt et d’émettre des notifications push de retour vers celui-ci.

Le deuxième prérequis, le dépôt **TeamUtilities**, est facultatif selon que votre équipe dispose d’un dépôt d’utilitaires qui lui est propre. Si une des trois autres conditions préalables n’est pas remplie, contactez votre responsable d’équipe, votre coordinateur du projet ou leurs délégués, pour qu’il l’exécute en suivant les instructions fournies pour les [Tâches du responsable d’une équipe de science des données](team-lead-tasks.md) ou pour les [ Tâches du coordinateur de projet pour une équipe de science des données](project-lead-tasks.md).

- Git doit être installé sur votre machine. Si vous utilisez une image Data Science Virtual Machine (DSVM), Git a été préinstallé et vous êtes prêt à commencer. Dans le cas contraire, consultez l’[annexe Plateformes et outils](platforms-and-tools.md#appendix).  
- Si vous utilisez une image **DSVM Windows**, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) doit être installé sur votre machine. Dans le fichier README.md, accédez à la section **Dowload and Install** (Téléchargement et installation), puis cliquez sur *latest installer* (programme d’installation le plus récent). Vous accédez à la page du programme d’installation le plus récent. Téléchargez le programme d’installation .exe et exécutez-le. 
- Si vous utilisez une image **DSVM Linux**, créez une clé publique SSH sur votre image DSVM et ajoutez-la à votre serveur VSTS de groupe. Pour plus d’informations sur SSH, consultez la section **Créer une clé publique SSH** de l’[annexe Plateformes et outils](platforms-and-tools.md#appendix). 
- Si le responsable d’équipe ou le coordinateur de projet a créé un stockage de fichiers Azure à monter sur votre image DSVM, vous devez vous procurer des informations sur ce stockage auprès de cette personne. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Étapes 1-3 : Cloner des dépôts de groupe, d’équipe et de projet sur une machine locale

Cette section fournit des instructions sur l’exécution des trois premières tâches asignées aux contributeurs individuels de projet : 

- Cloner le dépôt **GroupUtilities** R2 sur D2
- Cloner le dépôt **TeamUtilities** R4 sur D4 
- Cloner le dépôt **Project** R5 sur D5

Sur votre machine locale, créez un répertoire, ***C:\GitRepos*** (pour Windows) ou ***$home/GitRepos*** (pour Linux), puis changez de répertoire pour celui que vous venez de créer. 

Exécutez l’une des commandes suivantes (selon votre système d’exploitation) pour cloner vos dépôts **GroupUtilities**, **TeamUtilities** et **Project** sur les répertoires de votre machine locale : 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Vérifiez la présence des trois dossiers sous votre répertoire de projet.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Vérifiez la présence des trois dossiers sous votre répertoire de projet.

![5.](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Étapes 4-5 : Monter un stockage de fichiers Azure sur votre instance DSVM (facultatif)

Pour monter un stockage de fichiers Azure sur votre image DSVM, consultez les instructions détaillées à la section 4 de [Tâches du responsable d’une équipe de science des données](team-lead-tasks.md).

## <a name="next-steps"></a>Étapes suivantes

Les liens ci-dessous donnent accès à des descriptions plus détaillées des rôles et des tâches définis par le processus TDSP (Team Data Science Process) :

- [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md)
- [Tâches du responsable d’une équipe de science des données](team-lead-tasks.md)
- [Tâches du coordinateur de projet pour une équipe de science des données](project-lead-tasks.md)
- [Contributeurs individuels de projet pour une équipe de science des données](project-ic-tasks.md)

