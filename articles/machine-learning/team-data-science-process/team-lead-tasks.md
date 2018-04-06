---
title: Tâches du responsable de l’équipe Team Data Science Process - Azure | Microsoft Docs
description: Liste des tâches du responsable de l’équipe chargée d’un projet de science des données.
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
ms.openlocfilehash: 85589dfd60a32a28cecf427a7f5668dc36f8f5f2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="team-lead-tasks"></a>Tâches du responsable d’équipe

Cette rubrique dresse la liste des tâches qui incombent au responsable d’une équipe chargée d’un projet de science des données. L’objectif est d’établir un environnement de collaboration en équipe standard conforme au processus [TDSP](overview.md) (Team Data Science Process). Le processus TDSP est une méthodologie de science des données agile et itérative permettant de fournir des solutions d’analytique prédictive et des applications intelligentes de manière efficace. Il est conçu pour aider à améliorer la collaboration et l’apprentissage au sein de l’équipe. Ce processus rassemble les bonnes pratiques et les structures issues de Microsoft et de l’ensemble du secteur, qui sont nécessaires pour implémenter les initiatives en science des données destinées à aider les entreprises à tirer pleinement parti de leurs programmes d’analytique. Pour obtenir une description des rôles des membres de l’équipe de science des données et des tâches qui leur incombent dans le cadre de ce processus, consultez [Rôles et tâches du processus TDSP](roles-tasks.md).

Un **responsable d’équipe** gère l’équipe Science des données d’une entreprise. Cette équipe est constituée de plusieurs scientifiques des données. Si le service chargé de la science des données ne comprend que quelques scientifiques des données, une même personne peut jouer le rôle de **Responsable de groupe** et de **Responsable d’équipe**, ou il est possible que ces rôles soient délégués à un assistant. Cela dit, les tâches confiées restent les mêmes. Le flux de travail des tâches que doivent effectuer les chefs d’équipe pour configurer cet environnement est illustré ci-dessous :

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Les tâches des blocs 1 et 2 de la figure sont nécessaires si vous utilisez Visual Studio Team Services (VSTS) comme plateforme d’hébergement de code et si vous souhaitez disposer d’un projet d’équipe distinct pour votre équipe. Une fois ces tâches terminées, tous les dépôts de votre équipe peuvent être créés sous ce projet d’équipe. 

Une fois que le responsable de groupe a répondu aux prérequis de la section suivante, cinq tâches principales (certaines facultatives) sont à effectuer dans ce didacticiel. Ces tâches correspondent aux principales sections numérotées de cette rubrique :

1. Créer un **projet d’équipe** sur le serveur VSTS du groupe et deux dépôts d’équipe dans le projet :
    - **Le dépôt ProjectTemplate** 
    - **Le dépôt TeamUtilities**
2. Amorcer le dépôt **ProjectTemplate** à partir du dépôt **GroupProjectTemplate** qui a été configuré par votre responsable de groupe. 
3. Créer des données d’équipe et des ressources d’analytique :
    - Ajoutez les utilitaires spécifiques à l’équipe au dépôt **TeamUtilities**. 
    - (Facultatif) Créez un **stockage de fichiers Azure** à utiliser pour stocker les ressources de données qui peuvent être utiles à toute l’équipe. 
4. (Facultatif) Monter le stockage de fichiers Azure sur l’instance **DSVM** (Data Science Virtual Machine) du responsable d’équipe, et y ajouter des ressources de données.
5. Configurer le **contrôle de sécurité** en ajoutant des membres d’équipe et en configurant leurs privilèges.

>[AZURE.NOTE] Dans les instructions qui suivent, nous décrivons les étapes nécessaires pour configurer un environnement d’équipe TDSP à l’aide de VSTS. Nous indiquons comment accomplir ces tâches avec VSTS, car c’est de cette façon que nous implémentons le processus TDSP chez Microsoft. Si une autre plateforme d’hébergement de code est utilisée pour votre groupe, les tâches que doit effectuer le responsable d’équipe ne changent généralement pas. En revanche, c’est la façon dont vont s’effectuer ces tâches qui sera différente.

## <a name="repositories-and-directories"></a>Dépôts et répertoires

Dans cette rubrique, les noms des dépôts et des répertoires sont abrégés. Ces noms facilitent le suivi des opérations entre les dépôts et les répertoires. Cette notation (**R** pour les dépôts Git et **D** pour les répertoires locaux sur votre instance DSVM) est employée dans les sections suivantes :

- **R1** : dépôt **GroupProjectTemplate** dans Git qui est configuré par le responsable de groupe sur votre serveur de groupe VSTS.
- **R3** : dépôt **ProjectTemplate** dans Git que vous configurez.
- **R4** : dépôt **TeamUtilities** dans Git que vous configurez.
- **D1** : répertoire local cloné à partir de R1 et copié sur D3.
- **D3** : répertoire local cloné à partir de R3, personnalisé, puis copié de nouveau sur R3.
- **D4** : répertoire local cloné à partir de R4, personnalisé, puis copié de nouveau sur R4.

Dans ce didacticiel, les noms spécifiés pour les dépôts et les répertoires ont été fournis en supposant que votre objectif est de créer un projet distinct pour votre équipe qui appartient à un plus grand groupe chargé de la science des données. Toutefois, d’autres options s’offrent à vous en tant que responsable d’équipe :

- L’ensemble du groupe peut choisir de créer un seul projet d’équipe. Dans ce cas, tous les projets de toutes les équipes de science des données se trouveraient sous ce projet d’équipe. Pour cela, vous pouvez désigner un administrateur Git et lui demander de suivre les instructions pour créer un projet d’équipe. Ce scénario peut être valide pour :
    -  Un petit groupe chargé de la science des données qui ne comprend pas plusieurs équipes de science des données 
    -  Un plus grand groupe chargé de la science des données qui comprend plusieurs équipes de science des données et qui souhaite optimiser la collaboration entre les équipes à l’aide d’activités telles que la planification sprint au niveau du groupe. 
- Les équipes peuvent choisir d’avoir des modèles de projet ou des utilitaires spécifiques à l’équipe sous un même projet d’équipe pour l’ensemble du groupe. Dans ce cas, les responsables d’équipe doivent créer des dépôts de modèles de projet d’équipe et/ou des dépôts d’utilitaires d’équipe sous le même projet d’équipe. Nommez ces dépôts *<nom_équipe\>ProjectTemplate* et *<nom_équipe\>Utilities*, par exemple, *équipe_John_ProjectTemplate* et *équipe_John_Utilities*. 

Dans tous les cas, les responsables d’équipe doivent indiquer aux membres de leur équipe les dépôts de modèles et d’utilitaires à utiliser lors de la configuration et du clonage des dépôts du projet et des dépôts d’utilitaires. Les coordinateurs de projet doivent suivre la rubrique [Tâches du coordinateur de projet pour une équipe de science des données](project-lead-tasks.md) pour créer des dépôts de projet, que ce soit sous un ou plusieurs projets d’équipe. 


## <a name="0-prerequisites"></a>0. Prérequis


Pour satisfaire aux prérequis, les tâches attribuées à votre responsable de groupe et décrites dans [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md) doivent être effectuées. Pour résumer, avant de commencer les tâches du responsable d’équipe, voici les conditions qui doivent être remplies : 

- Votre **serveur de groupe VSTS** (ou compte de groupe situé sur une autre plateforme d’hébergement de code) a été configuré par votre responsable de groupe.
- Votre responsable de groupe a configuré votre **dépôt GroupProjectTemplate** (R1) sous votre compte de groupe sur la plateforme d’hébergement de code que vous prévoyez d’utiliser.
- Vous avez été **autorisé** à créer des dépôts dans votre compte de groupe pour votre équipe.
- Git doit être installé sur votre machine. Si vous utilisez une image Data Science Virtual Machine (DSVM), Git a été préinstallé et vous êtes prêt à commencer. Dans le cas contraire, consultez l’[annexe Plateformes et outils](platforms-and-tools.md#appendix).  
- Si vous utilisez une image **DSVM Windows**, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) doit être installé sur votre machine. Dans le fichier README.md, accédez à la section **Dowload and Install** (Téléchargement et installation), puis cliquez sur *latest installer* (programme d’installation le plus récent). Vous accédez à la page du programme d’installation le plus récent. Téléchargez le programme d’installation .exe et exécutez-le. 
- Si vous utilisez une image **DSVM Linux**, créez une clé publique SSH sur votre image DSVM et ajoutez-la à votre serveur VSTS de groupe. Pour plus d’informations sur SSH, consultez la section **Créer une clé publique SSH** de l’[annexe Plateformes et outils](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Créer un projet d’équipe et des dépôts

Effectuez cette étape si vous utilisez VSTS comme plateforme d’hébergement de code pour la gestion des versions et la collaboration. Dans cette section, vous allez créer trois artefacts sur le serveur VSTS de votre groupe :

- Le projet **MyTeam** dans VSTS
- Le dépôt **MyProjectTemplate** (**R3**) sur Git
- Le dépôt **MyTeamUtilities** (**R4**) sur Git

### <a name="create-the-myteam-project"></a>Créer le projet MyTeam

- Accédez à la page d’accueil du serveur VSTS de votre groupe dont l’URL est `https://<VSTS Server Name\>.visualstudio.com`. 
- Cliquez sur **Nouveau** pour créer un projet d’équipe. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- La fenêtre Créer un projet d’équipe vous invite à entrer le nom du projet (**MyTeam** dans cet exemple). Sélectionnez **Agile** comme **Modèle de processus** et **Git** comme **Gestion de version**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Cliquez sur **Créer un projet**. Votre projet d’équipe **MyTeam** est créé en moins d’une minute. 

- Une fois que le projet d’équipe **MyTeam** est créé, cliquez sur le bouton **Accéder au projet** pour être dirigé vers la page d’accueil de votre projet d’équipe. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Si vous voyez une fenêtre contextuelle intitulée **Félicitations**, cliquez sur **Ajouter du code** (le bouton rouge). Sinon, cliquez sur **Code** (le bouton jaune). Vous êtes alors dirigé vers la page du dépôt Git de votre projet d’équipe. 

    ![5.](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Créer le dépôt MyProjectTemplate (R3) sur Git

- Dans la page du dépôt Git de votre projet d’équipe, cliquez sur la flèche vers le bas en regard du dépôt **MyTeam**, puis sélectionnez **Gérer les dépôts**.

    ![6.](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Sous l’onglet **Gestion de version** du panneau de configuration de votre projet d’équipe, cliquez sur **MyTeam**, puis sélectionnez **Renommer le dépôt**. 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Dans la fenêtre **Renommer le dépôt MyTeam**, entrez un nouveau nom pour le dépôt. Pour cet exemple, entrez *MyTeamProjectTemplate*. Vous pouvez choisir quelque chose comme *<nom_équipe\>ProjectTemplate*. Cliquez sur **Renommer** pour continuer.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Créer le dépôt MyTeamUtilities (R4) sur Git

- Pour créer le nouveau dépôt *<nom_équipe\>Utilities* sous votre projet d’équipe, cliquez sur **Nouveau dépôt** sous l’onglet **Gestion de version**du panneau de configuration de votre projet d’équipe.  

    ![9.](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- Dans la fenêtre **Créer un dépôt** qui s’affiche, fournissez un nom pour ce dépôt. Dans cet exemple, nous allons utiliser le nom *MyTeamUtilities*, qui correspond à **R4** dans notre notation. Choisissez un nom tel que *<nom_équipe\>utilitaires*. Sélectionnez **Git** pour le **Type**. Ensuite, cliquez sur **Créer** pour continuer.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Vérifiez que les deux nouveaux dépôts Git se trouvent sous votre projet d’équipe **MyTeam**. Dans cet exemple : 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. Amorcer les dépôts ProjectTemplate et TeamUtilities de votre équipe

La procédure d’amorçage utilise les répertoires sur votre instance DSVM locale comme sites de préproduction intermédiaires. Si vous devez personnaliser vos dépôts **ProjectTemplate** et **TeamUtilities** pour répondre à certains besoins de l’équipe, vous pouvez le faire à l’avant-dernière étape de la procédure. Voici le récapitulatif des étapes utilisées pour amorcer le contenu des dépôts **MyTeamProjectTemplate** et **MyTeamUtilities** par une équipe de science des données. Chaque étape correspond à une sous-section de la procédure d’amorçage :

- Cloner le dépôt de groupe dans le répertoire local : le R1 d’équipe cloné dans le D1 local
- Cloner les dépôts de votre équipe dans les répertoires locaux : les R3 et R4 d’équipe clonés dans les D3 et D4 locaux
- Copier le contenu du modèle de projet du groupe dans le dossier d’équipe local : contenu de D1 copié dans D3
- (Facultatif) Personnalisation de D3 et D4
- Envoyer (push) le contenu du répertoire local vers les dépôts d’équipe : le contenu de D3 et D4 D3 ajouté aux R3 et R4 d’équipe.


### <a name="initialize-the-team-repositories"></a>Initialiser les dépôts d’équipe

Dans cette étape, vous allez initialiser le dépôt de modèle de projet de votre équipe à partir du dépôt de modèle de projet de groupe :

- Le dépôt **MyTeamProjectTemplate** (**R3**) à partir de votre dépôt **GroupProjectTemplate** (**R1**)


### <a name="clone-group-repositories-into-local-directories"></a>Cloner des dépôts de groupe dans les répertoires locaux

Pour commencer cette procédure :

- Sur votre ordinateur local, créez des répertoires :
    - Pour **Windows** : **C:\GitRepos\GroupCommon** et **C:\GitRepos\MyTeam**
    - Pour **Linux** : **GitRepos\GroupCommon** et **GitRepos\MyTeam** dans votre répertoire de base 
- Remplacez le répertoire par le répertoire **GitRepos\GroupCommon**.
- Exécutez la commande suivante sur le système d’exploitation de votre ordinateur local.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13.](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Ces commandes clonent le dépôt **GroupProjectTemplate** (R1) de votre serveur VSTS de groupe dans un répertoire local situé sous **GitRepos\GroupCommon** sur votre ordinateur local. Après le clonage, le répertoire **GroupProjectTemplate** (D1) est créé dans le répertoire **GitRepos\GroupCommon**. Ici, nous partons du principe que le responsable de groupe a créé le projet d’équipe **GroupCommon** et le dépôt **GroupProjectTemplate** sous ce projet d’équipe. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Cloner les dépôts de votre équipe dans les répertoires locaux

Ces commandes clonent les dépôts **MyTeamProjectTemplate** (R3) et **MyTeamUtilities** (R4) de votre projet d’équipe **MyTeam** situé sur votre serveur VSTS de groupe, dans les répertoires **MyTeamProjectTemplate** (D3) et **MyTeamUtilities** (D4) situés sous **GitRepos\MyTeam** sur votre ordinateur local. 

- Remplacez le répertoire par le répertoire **GitRepos\MyTeam**.
- Exécutez les commandes suivantes sur le système d’exploitation de votre ordinateur local. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Après le clonage, les deux répertoires **MyTeamProjectTemplate** (D3) et **MyTeamUtilities** (D4) sont créés dans le répertoire **GitRepos\MyTeam**. Nous supposons ici que vous avez nommé votre dépôt de modèle de projet d’équipe et votre dépôt d’utilitaires **MyTeamProjectTemplate** et **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>Copier le contenu du modèle de projet du groupe dans le répertoire local du modèle de projet de l’équipe

Pour copier le contenu du dossier local **GroupProjectTemplate** (D1) dans le répertoire local **MyTeamProjectTemplate** (D3), exécutez l’un des scripts d’interpréteur de commandes suivants : 

#### <a name="from-the-powershell-command-line-for-windows"></a>À partir de la ligne de commande PowerShell pour Windows       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>À partir du shell Linux pour une instance **DSVM Linux**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Les scripts excluent le contenu du dépôt .git. Les scripts vous invitent à fournir le **chemin complet** du répertoire source D1 et du répertoire de destination D3.
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Personnaliser votre dépôt de modèle de projet d’équipe ou d’utilitaires d’équipe (facultatif)

Si vous souhaitez personnaliser vos dépôts **MyTeamProjectTemplate** (D3) et **MyTeamUtilities** (D4), c’est à ce stade du processus de configuration que vous devez le faire. 

- Si vous souhaitez personnaliser le contenu de D3 pour répondre aux besoins de votre équipe, vous pouvez modifier les documents de modèle ou modifier la structure de répertoires.

- Si votre équipe a développé des utilitaires que vous souhaitez partager avec toute l’équipe, copiez-collez ces utilitaires dans le répertoire D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Envoyer (push) le contenu d’un répertoire local vers les dépôts de l’équipe

Pour ajouter le contenu des répertoires locaux (et éventuellement personnalisés) D3 et D4 aux dépôts d’équipe R3 et R4, exécutez les commandes Bash Git suivantes à partir d’une console Windows PowerShell ou du shell Linux. Exécutez les commandes à partir du répertoire **GitRepos\MyTeam\MyTeamProjectTemplate**.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Les fichiers du dépôt MyTeamProjectTemplate situé sur le serveur VSTS de votre groupe sont synchronisés de manière quasi instantanée lorsque ce script est exécuté.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Maintenant, exécutez les mêmes quatre commandes Git à partir du répertoire **GitRepos\MyTeam\MyTeamUtilities**. 

> [AZURE.NOTE]S’il s’agit de votre première validation dans un dépôt Git, vous devez configurer les paramètres globaux *user.name* et *user.email* avant d’exécuter la commande `git commit`. Exécutez les deux commandes suivantes :
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Si la validation porte sur plusieurs dépôts Git, utilisez le même nom et la même adresse e-mail quand vous validez dans chacun d’eux. L’utilisation du même nom et de la même adresse e-mail s’avère plus pratique par la suite, au moment de créer des tableaux de bord PowerBI pour suivre vos activités Git sur plusieurs dépôts.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Créer des données d’équipe et des ressources d’analytique (facultatif)

Le partage des ressources de données et d’analytique avec toute votre équipe présente des avantages au niveau des performances et des coûts : les membres de l’équipe peuvent exécuter leurs projets sur les ressources partagées, économiser sur le budget et collaborer plus efficacement. Cette section comprend des instructions sur la création d’un stockage de fichiers Azure. La section suivante comprend des instructions sur le montage du stockage de fichiers Azure sur votre ordinateur local. Pour plus d’informations sur le partage d’autres ressources, telles que les instances Azure Data Science Virtual Machine ou les clusters Azure HDInsight Spark, consultez [Plateformes et outils](platforms-and-tools.md). Cette rubrique vous conseille sur les ressources à sélectionner pour vos besoins en science des données, et fournit des liens vers des pages produits et d’autres didacticiels utiles que nous avons publiés.

>[AZURE.NOTE] Pour éviter que les données ne soient transmises d’un centre de données à l’autre, ce qui peut entraîner des problèmes de lenteur et se révéler coûteux, placez le groupe de ressources, le compte de stockage et la machine virtuelle Azure (par exemple, DSVM) au sein du même centre de données Azure. 

Exécutez les scripts suivants pour créer le stockage de fichiers Azure de votre équipe. Le stockage de fichiers Azure de votre équipe peut être utilisé pour stocker les ressources de données qui peuvent être utiles à toute l’équipe. Les scripts vous invitent à entrer vos informations de compte et d’abonnement Azure. Veillez donc à avoir ces informations sous la main lorsque vous exécutez les scripts. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Créer le stockage de fichiers Azure avec PowerShell à partir de Windows

Exécutez ce script à partir de la ligne de commande PowerShell :

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Connectez-vous à votre compte Microsoft Azure lorsque vous y êtes invité :

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Sélectionnez l’abonnement Azure à utiliser :

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Sélectionnez le compte de stockage à utiliser ou créez en un sous l’abonnement sélectionné :

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Entrez le nom du stockage de fichiers Azure à créer. Seuls les minuscules, les chiffres et les tirets (-) sont acceptés :

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Pour faciliter le montage et le partage de ce stockage après sa création, enregistrez les informations le concernant dans un fichier texte, et notez le chemin de son emplacement. Vous aurez besoin de ce fichier pour monter le stockage de fichiers Azure de vos machines virtuelles Azure dans la section suivante. 

Il est conseillé d’archiver ce fichier texte dans le dépôt ProjectTemplate de votre équipe. Nous recommandons de le placer dans le répertoire **Docs\DataDictionaries**. Ainsi, cette ressource de données est accessible par tous les projets de votre équipe. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Créer le stockage de fichiers Azure avec un script Linux

Exécutez ce script à partir du shell Linux :

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Connectez-vous à votre compte Microsoft Azure en suivant les instructions qui s’affichent à l’écran :

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Sélectionnez l’abonnement Azure à utiliser :

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Sélectionnez le compte de stockage à utiliser ou créez en un sous l’abonnement sélectionné :

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Entrez le nom du stockage de fichiers Azure à créer (seuls les minuscules, les chiffres et les tirets (-) sont acceptés) :

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Pour faciliter l’accès à ce stockage après sa création, enregistrez les informations le concernant dans un fichier texte, et notez le chemin de son emplacement. Vous aurez besoin de ce fichier pour monter le stockage de fichiers Azure de vos machines virtuelles Azure dans la section suivante.

Il est conseillé d’archiver ce fichier texte dans le dépôt ProjectTemplate de votre équipe. Nous recommandons de le placer dans le répertoire **Docs\DataDictionaries**. Ainsi, cette ressource de données est accessible par tous les projets de votre équipe. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Monter le stockage de fichiers Azure (facultatif)

Une fois que vous avez créé le stockage de fichiers Azure, vous pouvez le monter sur votre ordinateur local à l’aide de l’un des scripts PowerShell ou Linux suivants.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Monter le stockage de fichiers Azure avec PowerShell à partir de Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Si vous n’êtes pas connecté, vous êtes d’abord invité à ouvrir une session. 

Pour continuer, appuyez sur la touche **Entrée** ou **y** lorsqu’il vous est demandé d’indiquer si vous disposez d’un fichier d’informations sur le stockage de fichiers Azure. Ensuite, entrez ***le chemin complet et le nom** du fichier que vous avez créé à l’étape précédente. Les informations nécessaires au montage du stockage de fichiers Azure sont lues directement dans ce fichier, ce qui vous permet de passer à l’étape suivante.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Si vous ne disposez pas d’un fichier contenant les informations du stockage de fichiers Azure, consultez les étapes à la fin de cette section qui permettent d’entrer ces informations à partir du clavier.

Ensuite, vous êtes invité à entrer le nom du lecteur qui doit être ajouté à votre machine virtuelle. La liste des lecteurs existants s’affiche à l’écran. Vous devez fournir un nom de lecteur qui ne se trouve pas dans cette liste.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Vérifiez que le nouveau lecteur F a été monté correctement sur votre machine.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Comment entrer manuellement les informations relatives au stockage de fichiers Azure :** si les informations concernant le stockage de fichiers Azure ne se trouvent pas dans un fichier texte, suivez les instructions de l’écran ci-dessous pour entrer l’abonnement, le compte de stockage et les informations relatives au stockage de fichiers Azure :

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Tapez le nom de votre abonnement Azure, sélectionnez le compte de stockage dans lequel a été créé le stockage de fichiers Azure, puis tapez le nom du stockage de fichiers Azure :

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Monter le stockage de fichiers Azure avec un script Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Si vous n’êtes pas connecté, vous êtes d’abord invité à ouvrir une session. 

Pour continuer, appuyez sur la touche **Entrée** ou **y** lorsqu’il vous est demandé d’indiquer si vous disposez d’un fichier d’informations sur le stockage de fichiers Azure. Ensuite, entrez ***le chemin complet et le nom** du fichier que vous avez créé à l’étape précédente. Les informations nécessaires au montage du stockage de fichiers Azure sont lues directement dans ce fichier, ce qui vous permet de passer à l’étape suivante.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Ensuite, vous êtes invité à entrer le nom du lecteur qui doit être ajouté à votre machine virtuelle. La liste des lecteurs existants s’affiche à l’écran. Vous devez fournir un nom de lecteur qui ne se trouve pas dans cette liste.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Vérifiez que le nouveau lecteur F a été monté correctement sur votre machine.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Comment entrer manuellement les informations relatives au stockage de fichiers Azure :** si les informations concernant le stockage de fichiers Azure ne se trouvent pas dans un fichier texte, suivez les instructions de l’écran ci-dessous pour entrer l’abonnement, le compte de stockage et les informations relatives au stockage de fichiers Azure :

- Tapez **n**.
- Sélectionnez l’index du nom de l’abonnement dans lequel le stockage de fichiers Azure a été créé à l’étape précédente :

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Sélectionnez le compte de stockage sous votre abonnement et tapez le nom du stockage de fichiers Azure :

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Entrez le nom du lecteur à ajouter à votre ordinateur (ce lecteur ne doit pas déjà se trouver sur l’ordinateur):

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Configurer la stratégie de contrôle de la sécurité 

À partir de la page d’accueil de votre serveur VSTS de groupe, cliquez sur **l’icône d’engrenage** en regard de votre nom d’utilisateur dans le coin supérieur droit, puis sélectionnez l’onglet **Sécurité**. Vous pouvez ajouter des membres à votre équipe avec différents niveaux d’autorisations.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Étapes suivantes

Les liens ci-dessous donnent accès à des descriptions plus détaillées des rôles et des tâches définis par le processus TDSP (Team Data Science Process) :

- [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md)
- [Tâches du responsable d’une équipe de science des données](team-lead-tasks.md)
- [Tâches du coordinateur de projet pour une équipe de science des données](project-lead-tasks.md)
- [Contributeurs individuels de projet pour une équipe de science des données](project-ic-tasks.md)