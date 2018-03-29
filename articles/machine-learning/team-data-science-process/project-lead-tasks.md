---
title: Tâches du coordinateur de projet Team Data Science Process - Azure | Microsoft Docs
description: Vue d’ensemble des tâches d’un coordinateur de projet dans une équipe de science des données.
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
ms.openlocfilehash: 5c769fda09e70cfb0a43e0305ffab4923fbe3307
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="project-lead-tasks"></a>Tâches du coordinateur de projet

Ce didacticiel décrit de façon succincte les tâches qu’un coordinateur de projet est censé effectuer pour son équipe de projet. L’objectif est d’établir un environnement d’équipe collaborative standard conforme à [TDSP](overview.md) (Team Data Science Process). TDSP est un cadre élaboré par Microsoft qui propose une succession structurée d’activités visant à exécuter des solutions d’analytique prédictive basées sur le cloud avec efficacité. Pour obtenir une description sommaire des différents rôles au sein de l’équipe de science des données et des tâches qui incombent à ses membres dans le cadre de ce processus, consultez [Rôles et tâches du processus TDSP](roles-tasks.md).

Un **coordinateur de projet** gère les activités quotidiennes des différents scientifiques des données dans le cadre d’un projet de science des données spécifique. Le flux de travail correspondant aux tâches que doit effectuer le coordinateur pour configurer cet environnement est illustré dans le schéma suivant :

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Cette rubrique couvre actuellement les tâches 1, 2 et 6 de ce flux de travail pour coordinateurs de projet.

>[AZURE.NOTE] Dans les instructions suivantes, nous décrivons brièvement les étapes à suivre pour configurer un environnement d’équipe TDSP pour un projet utilisant Visual Studio Team Services (VSTS). Nous indiquons comment effectuer ces tâches avec VSTS, car c’est ainsi que nous implémentons TDSP chez Microsoft. Si une autre plateforme d’hébergement de code est utilisée pour votre groupe, les tâches que doit effectuer le responsable d’équipe ne changent généralement pas. En revanche, c’est la façon dont vont s’effectuer ces tâches qui sera différente.


## <a name="repositories-and-directories"></a>Dépôts et répertoires

Dans ce didacticiel, les noms des dépôts et des répertoires sont abrégés. Cela facilite le suivi des opérations entre les dépôts et les répertoires. Cette notation (R pour les dépôts Git et D pour les répertoires locaux sur votre DSVM) est employée dans les sections suivantes :

- **R3** : dépôt Git **ProjectTemplate** de l’équipe que votre responsable d’équipe a configuré.
- **R5** : dépôt Git du projet que vous avez configuré pour votre projet.
- **D3** : répertoire local cloné à partir de R3.
- **D5** : le répertoire local cloné à partir de R5.


## <a name="0-prerequisites"></a>0. Prérequis


Pour satisfaire les prérequis, il convient de mener à bien les tâches affectées à votre responsable de groupe décrites dans [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md), de même que celles affectées à votre responsable d’équipe décrites dans [Coordinateur d’équipe pour une équipe de science des données](team-lead-tasks.md). 

Pour résumer, avant de commencer les tâches du responsable d’équipe, voici les conditions à remplir : 

- Votre **serveur de groupe VSTS** (ou compte de groupe sur une autre plateforme d’hébergement de code) a été configuré par votre responsable de groupe.
- Votre responsable d’équipe a configuré votre **dépôt TeamProjectTemplate** (R3) sous votre compte de groupe sur la plateforme d’hébergement de code que vous prévoyez d’utiliser.
- Votre responsable d’équipe vous a **autorisé** à créer des dépôts sur votre compte de groupe pour votre équipe.
- Git doit être installé sur votre ordinateur. Si vous utilisez une image Data Science Virtual Machine (DSVM), Git a été préinstallé et vous êtes prêt à commencer. Dans le cas contraire, consultez l’[annexe Plateformes et outils](platforms-and-tools.md#appendix).  
- Si vous utilisez une image **DSVM Windows**, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) doit être installé sur votre machine. Dans le fichier README.md, accédez à la section **Dowload and Install** (Téléchargement et installation), puis cliquez sur *latest installer* (programme d’installation le plus récent). Vous accédez à la page du programme d’installation le plus récent. Téléchargez le programme d’installation .exe et exécutez-le. 
- Si vous utilisez une image **DSVM Linux**, créez une clé publique SSH sur votre image DSVM et ajoutez-la à votre serveur VSTS de groupe. Pour plus d’informations sur SSH, consultez la section **Créer une clé publique SSH** de l’[annexe Plateformes et outils](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Créer un dépôt de projet (R5)

- Connectez-vous au serveur VSTS de votre groupe à l’adresse *https://\<Nom du serveur VSTS\>.visualstudio.com*. 
- Sous **Projets et équipes récents**, cliquez sur **Parcourir**. Une fenêtre s’affiche avec tous les projets d’équipe présents sur le serveur VSTS. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Cliquez sur le nom du projet d’équipe dans lequel vous allez créer le dépôt de votre projet. En l’occurrence, cliquez sur **MyTeam**. 
- Cliquez ensuite sur **Accéder** pour être dirigé vers la page d’accueil du projet d’équipe **MyTeam** :

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Cliquez sur **Collaborer autour du code** pour être dirigé vers la page d’accueil Git de votre projet d’équipe.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Cliquez sur la flèche vers le bas dans l’angle supérieur gauche, puis sélectionnez **+ Nouveau référentiel**. 
    
    ![5.](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- Dans la fenêtre **Créer un référentiel**, attribuez un nom au dépôt Git de votre projet. Veillez à sélectionner **Git** comme type de référentiel. Dans cet exemple, nous utilisons le nom *DSProject1*. 

    ![6.](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Pour créer votre dépôt Git de projet ***DSProject1***, cliquez sur **Créer**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Amorcer le dépôt de projet DSProject1

Cette tâche consiste à amorcer le dépôt de projet **DSProject1** (R5) à partir de votre dépôt de modèles de projet d’équipe (R3). La procédure d’amorçage utilise les répertoires D3 et D5 sur votre instance DSVM locale comme sites intermédiaires. En résumé, le chemin d’amorçage est : R3 -> D3 -> D5 -> R5.

Si vous devez personnaliser votre dépôt de projet **DSProject1** pour répondre à certaines besoins spécifiques du projet, vous pouvez le faire à l’avant-dernière étape de la procédure. Voici un résumé des étapes à suivre pour amorcer le contenu du dépôt de projet **DSProject1**. Les étapes individuelles correspondent aux sous-sections de la procédure d’amorçage :

- Clonez le dépôt de modèles de projet d’équipe dans le répertoire local : R3 équipe - cloné dans -> D3 local.
- Clonez le dépôt DSProject1 dans un répertoire local : R5 équipe - cloné dans -> D5 local.
- Copiez le contenu de modèles de projet d’équipe cloné dans le clone local du dépôt DSProject1 : D3 - contenu copié dans -> D5.
- (Facultatif) Personnalisez le répertoire D5 local.
- Envoyez (push) le contenu local de DSProject1 vers les dépôts de l’équipe : D5 - contenu ajouté à -> R5 équipe.


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Clonez votre dépôt de modèles de projet d’équipe (R3) dans un répertoire (D3) sur votre ordinateur local.

Sur votre ordinateur local, créez un répertoire :

- *C:\GitRepos\MyTeamCommon* pour Windows 
- *$home/GitRepos/MyTeamCommon* pour Linux

Spécifiez ce répertoire. Exécutez ensuite la commande suivante pour cloner votre dépôt de modèles de projet d’équipe sur votre ordinateur local. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Si vous utilisez VSTS comme plateforme d’hébergement de code, l’*URL HTTPS de votre dépôt de modèles de projet d’équipe* se présente généralement sous la forme suivante :

 ***https://\<Nom du serveur VSTS\>.visualstudio.com/\<Nom de votre projet d’équipe\>/_git/\<Nom de votre dépôt de modèles de projet d’équipe\>***. 

Pour cet exemple, cela donne :

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Si vous utilisez VSTS comme plateforme d’hébergement de code, l’*URL SSH du dépôt de modèles de projet d’équipe* se présente généralement sous la forme suivante :

***ssh://\<Nom du serveur VSTS\>@\<Nom du serveur VSTS\>.visualstudio.com:22/\<Nom de votre projet d’équipe>/_git/\<Nom de votre dépôt de modèles de projet d’équipe\>.*** 

Pour cet exemple, cela donne :

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Cloner le dépôt DSProject1 (R5) dans un répertoire (D5) sur votre ordinateur local

Spécifiez le répertoire **GitRepos**, puis exécutez la commande suivante pour cloner votre dépôt de projet sur votre ordinateur local. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9.](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Si vous utilisez VSTS comme plateforme d’hébergement de code, l’_URL HTTPS du dépôt de projet_ se présente généralement sous la forme ***https://\<Nom du serveur VSTS\>.visualstudio.com/\<Nom de votre projet d’équipe>/_git/<Nom de votre dépôt de projet\>***. Pour cet exemple, cela donne ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Si vous utilisez VSTS comme plateforme d’hébergement de code, l’_URL SSH du dépôt de projet_ se présente généralement sous la forme _ssh://<Nom du serveur VSTS\>@<Nom du serveur VSTS\>.visualstudio.com:22/<Your Team Project Name>/\_git/<Nom de votre dépôt de projet\>. Pour cet exemple, cela donne ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Copier le contenu de D3 vers D5 

Maintenant, sur votre ordinateur local, vous devez copier le contenu de _D3_ vers _D5_, à l’exception des métadonnées Git, dans le répertoire .git. Les scripts suivants effectueront cette opération. Veillez à taper correctement les chemins complets des répertoires. Le dossier source est celui de votre équipe (_D3_) ; le dossier de destination est celui de votre projet (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Comme vous pouvez le constater dans le dossier _DSProject1_, tous les fichiers (à l’exclusion du répertoire .git) sont maintenant copiés à partir de _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13.](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Comme vous pouvez le constater dans le dossier _DSProject1_, tous les fichiers (à l’exclusion des métadonnées contenues dans .git) sont maintenant copiés à partir de _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Personnaliser D5 si nécessaire (facultatif)

Si votre projet nécessite certains répertoires ou documents spécifiques, autres que ceux que vous obtenez de votre modèle de projet d’équipe (copiés dans votre répertoire D5 à l’étape précédente), vous pouvez personnaliser le contenu de D5 à ce stade. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>Ajouter le contenu de DSProject1 de D5 vers R5 sur le serveur VSTS de votre groupe

Vous devez maintenant envoyer (push) le contenu de  **_DSProject1_** vers le dépôt _R5_ de votre projet d’équipe sur le serveur VSTS de votre groupe. 


- Spécifiez le répertoire **D5**. 
- Utilisez les commandes Git suivantes pour ajouter le contenu de **D5** à **R5**. Les commandes sont les mêmes pour les systèmes Windows et Linux. 
    
    git status git add .
    git commit -m"push from win DSVM" git push
    
- Validez la modification et l’envoi (push). 

>[AZURE.NOTE] S’il s’agit de votre première validation dans un dépôt Git, vous devez configurer les paramètres globaux *user.nom* et *user.email* avant d’exécuter la commande `git commit`. Exécutez les deux commandes suivantes :
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Si la validation porte sur plusieurs dépôts Git, utilisez le même nom et la même adresse e-mail pour tous. L’utilisation du même nom et de la même adresse e-mail s’avère plus pratique par la suite, au moment de créer des tableaux de bord PowerBI pour suivre vos activités Git sur plusieurs dépôts.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Créer et monter un stockage de fichiers Azure comme ressources de projet (facultatif)

Si vous voulez créer un stockage de fichiers Azure pour partager des données, par exemple les données brutes du projet ou les fonctionnalités générées pour votre projet, et ainsi permettre à tous les membres du projet d’accéder aux mêmes jeux de données à partir de plusieurs DSVM, suivez les instructions des sections 3 et 4 de la rubrique [Tâches du responsable d’une équipe de science des données](team-lead-tasks.md). 


## <a name="next-steps"></a>Étapes suivantes

Les liens ci-dessous donnent accès à des descriptions plus détaillées des rôles et des tâches définis par le processus TDSP (Team Data Science Process) :

- [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md)
- [Tâches du responsable d’une équipe de science des données](team-lead-tasks.md)
- [Tâches du coordinateur de projet pour une équipe de science des données](project-lead-tasks.md)
- [Contributeurs individuels de projet pour une équipe de science des données](project-ic-tasks.md)