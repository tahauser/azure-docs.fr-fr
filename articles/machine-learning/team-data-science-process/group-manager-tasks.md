---
title: Tâches du responsable de groupe Team Data Science Process - Azure | Microsoft Docs
description: Liste des tâches d’un responsable de groupe chargé d’un projet d’équipe de science des données.
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
ms.openlocfilehash: 3c39ae865ec26567f70706b042dab2aa8e4939d3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="group-manager-tasks"></a>Tâches du responsable de groupe

Cette rubrique dresse la liste des tâches qui incombent à un responsable de groupe chargé d’une organisation de science des données. L’objectif est d’établir un environnement de collaboration en groupe standard conforme au processus [TDSP](overview.md) (Team Data Science Process). Pour obtenir une description des rôles des membres de l’équipe de science des données et des tâches qui leur incombent dans le cadre de ce processus, consultez [Rôles et tâches du processus TDSP](roles-tasks.md).

Le **responsable de groupe** est le responsable de l’ensemble de l’unité de science des données dans une entreprise. Une unité de science des données peut avoir plusieurs équipes, chacune travaillant sur plusieurs projets de science des données dans différents secteurs d’activité. Un responsable de groupe peut déléguer ses tâches à un remplaçant, mais les tâches associées au rôle sont les mêmes. Il existe six tâches principales comme indiqué dans le diagramme suivant :

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Dans les instructions qui suivent, nous décrivons les étapes nécessaires pour configurer un environnement de groupe TDSP à l’aide de VSTS. Nous indiquons comment accomplir ces tâches avec VSTS, car c’est de cette façon que nous implémentons le processus TDSP chez Microsoft. Si une autre plateforme d’hébergement de code est utilisée pour votre groupe, les tâches que doit effectuer le responsable de groupe ne changent généralement pas. En revanche, c’est la façon dont vont s’effectuer ces tâches qui va être différente.

1. Configurer le **serveur Visual Studio Team Services (VSTS)** pour le groupe.
2. Créer un **projet d’équipe de groupe** sur le serveur de Visual Studio Team Services (pour les utilisateurs de VSTS).
3. Créer le dépôt **GroupProjectTemplate**.
4. Créer le dépôt **GroupUtilities**.
5. Amorcer les dépôts **GroupProjectTemplate** et **GroupUtilities** pour le serveur VSTS avec du contenu issu des référentiels TDSP.
6. Configurer les **contrôles de sécurité** pour que les membres de l’équipe puissent accéder aux dépôts GroupProjectTemplate et GroupUtilities.

Chacune des étapes ci-dessus est décrite en détail. Mais tout d’abord, nous allons vous familiariser avec les abréviations et décrire les prérequis pour utiliser les référentiels.

### <a name="abbreviations-for-repositories-and-directories"></a>Abréviations des dépôts et des référentiels

Dans ce didacticiel, les noms des référentiels et des répertoires sont abrégés. Ces définitions facilitent le suivi des opérations entre les référentiels et les répertoires. Cette notation est utilisée dans les sections suivantes :

- **G1** : référentiel de modèles de projet développé et géré par l’équipe TDSP de Microsoft
- **G2** : référentiel d’utilitaires développé et géré par l’équipe TDSP de Microsoft
- **R1** : dépôt GroupProjectTemplate sur Git que vous configurez sur votre serveur de groupe VSTS
- **R2** : dépôt GroupUtilities sur Git que vous configurez sur votre serveur de groupe VSTS
- **LG1** et **LG2** : répertoires locaux sur votre machine dans lesquels vous clonez G1 et G2, respectivement.
- **LR1** et **LR2** : répertoires locaux sur votre machine dans lesquels vous clonez R1 et R2, respectivement.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Prérequis pour le clonage des référentiels et l’archivage et l’extraction de code
 
- GIT doit être installé sur votre ordinateur. Si vous utilisez une image Data Science Virtual Machine (DSVM), Git a été préinstallé et vous êtes prêt à commencer. Dans le cas contraire, consultez l’[annexe Plateformes et outils](platforms-and-tools.md#appendix).  
- Si vous utilisez une image **DSVM Windows**, [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) doit être installé sur votre machine. Dans le fichier README.md, accédez à la section **Dowload and Install**, puis cliquez sur *latest installer*. Vous accédez alors à la page du programme d’installation le plus récent. Téléchargez le programme d’installation .exe et exécutez-le. 
- Si vous utilisez une image **DSVM Linux**, créez une clé publique SSH sur votre image DSVM et ajoutez-la à votre serveur VSTS de groupe. Pour plus d’informations sur SSH, consultez la section **Créer une clé publique SSH** de l’[annexe Plateformes et outils](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-vsts-server"></a>1. Créer un compte sur le serveur VSTS

Le serveur VSTS héberge les référentiels suivants :

- **Référentiels de groupe communs** : dépôts à usage général qui peuvent être adoptés par plusieurs équipes au sein d’un groupe pour plusieurs projets de science des données. Par exemple, les dépôts *GroupProjectTemplate* et *GroupUtilities*.
- **Référentiels d’équipe** : dépôts pour des équipes spécifiques au sein d’un groupe. Ces référentiels sont spécifiques au besoin d’une équipe et peuvent être adoptés par plusieurs projets exécutés par cette équipe, mais ne sont pas suffisamment généraux pour servir à plusieurs équipes au sein d’un groupe de science des données. 
- **Référentiels de projet**  : dépôts disponibles pour des projets spécifiques. Ces dépôts peuvent ne pas être suffisamment généraux pour être utiles pour plusieurs projets menés par une équipe et pour plusieurs équipes dans un groupe de science des données.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Configuration de la connexion au serveur VSTS depuis votre compte Microsoft
    
Accédez à [Visual Studio Online](https://www.visualstudio.com/), cliquez sur **Se connecter** dans le coin supérieur droit et connectez-vous à votre compte Microsoft. 
    
![1](./media/group-manager-tasks/login.PNG)

Si vous n’avez pas de compte Microsoft, cliquez sur **Inscrivez-vous maintenant** pour créer un compte Microsoft, puis connectez-vous à l’aide de ce compte. 

Si votre organisation dispose d’un abonnement Visual Studio/MSDN, cliquez sur l’option **Se connecter avec votre compte professionnel ou scolaire** (encadrée en vert ci-dessous) et connectez-vous avec les informations d’identification associées à cet abonnement. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Après vous être connecté, cliquez sur **Créer un compte** dans le coin supérieur droit, comme illustré dans l’image suivante :
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Renseignez les informations relatives au serveur VSTS que vous souhaitez créer dans l’Assistant **Créer votre compte** avec les valeurs suivantes : 

- **URL du serveur** : remplacez *mysamplegroup* par le *nom de votre serveur*. L’URL de votre serveur sera *https://\<nom_serveur\>.visualstudio.com*. 
- **Gérer le code à l’aide de :** sélectionnez **_Git_**.
- **Nom du projet :** entrez *GroupCommon*. 
- **Organiser le travail via :** choisissiez *Agile*.
- **Héberger vos projets dans :** choisissez un emplacement géographique. Dans cet exemple, nous avons choisi *Centre-Sud des États-Unis*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Si la fenêtre indépendante suivante apparaît une fois que vous avez cliqué sur **Créer un compte**, vous devez cliquer sur **Changer les détails** pour afficher tous les champs détaillés.

![5.](./media/group-manager-tasks/create-account-2.png)


Cliquez sur **Continuer**. 

## <a name="2-groupcommon-team-project"></a>2. Projet d’équipe GroupCommon

La page **GroupCommon** (*https://\<nom_serveur\>.visualstudio.com/GroupCommon*) s’ouvre une fois votre serveur VSTS créé.
                            
![6.](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Créer le dépôt GroupUtilities (R2)

Pour créer le dépôt **GroupUtilities** (R2) sous le serveur VSTS :

- Pour ouvrir l’Assistant **Créer un référentiel**, cliquez sur **Nouveau référentiel** sous l’onglet **Gestion de version** de votre projet d’équipe. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Sélectionnez *Git* comme **Type**et entrez *GroupUtilities* comme **Nom**, puis cliquez sur **Créer**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Maintenant vous devez voir deux dépôts Git (**GroupProjectTemplate** et **GroupUtilities**) dans la colonne de gauche de la page **Gestion de version** : 

![9.](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Créer le dépôt GroupProjectTemplate (R1)

La configuration des dépôts pour le serveur de groupe VSTS se compose de deux tâches :

- Renommer le dépôt par défaut **GroupCommon** ***GroupProjectTemplate***.
- Créer le dépôt **GroupUtilities** sur le serveur VSTS sous le projet d’équipe **GroupCommon**. 

Vous trouverez les instructions pour la première tâche dans cette section, après les remarques sur les conventions de nommage pour nos référentiels et répertoires. Les instructions pour la seconde tâche se trouvent dans la section suivante pour l’étape 4.

### <a name="rename-the-default-groupcommon-repository"></a>Renommer le dépôt GroupCommon par défaut

Pour renommer le dépôt **GroupCommon** par défaut *GroupProjectTemplate* (appelé **R1** dans ce didacticiel) :
    
- Cliquez sur **Collaborer autour du code** dans la page de projet d’équipe **GroupCommon**. Vous accédez ainsi à la page de dépôt Git par défaut du projet d’équipe **GroupCommon**. Ce dépôt Git est vide. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Cliquez sur l’option **GroupCommon** (encadrée en rouge dans la figure suivante) dans le coin supérieur gauche de la page de dépôt Git de **GroupCommon**, puis sélectionnez l’option **Gérer les référentiels** (encadrée en vert dans la figure suivante). Cette procédure permet d’afficher le **Panneau de configuration**. 
- Sélectionnez l’onglet **Gestion de version** de votre projet d’équipe. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Cliquez sur **...** à droite du dépôt **GroupCommon** dans le volet gauche, puis sélectionnez **Renommer le référentiel**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- Dans l’Assistant **Renommer le référentiel GroupCommon** qui s’affiche, entrez *GroupProjectTemplate* dans la zone **Nom du référentiel**, puis cliquez sur **Renommer**. 

![13.](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. Amorcer les dépôts R1 et R2 sur le serveur VSTS

Dans cette étape de la procédure, vous amorcez les dépôts *GroupProjectTemplate* (R1) et *GroupUtilities* (R2) que vous avez configurés dans la section précédente. Ces dépôts sont amorcés avec les référentiels ***ProjectTemplate*** (**G1**) et ***Utilities*** (**G2**) qui sont gérés par Microsoft pour le processus TDSP. Quand cet amorçage est terminé :

- votre dépôt R1 a le même ensemble de répertoires et de modèles de document que le référentiel G1 ;
- votre dépôt R2 contient l’ensemble des utilitaires de science des données développés par Microsoft.

La procédure d’amorçage utilise les répertoires sur votre instance DSVM locale comme sites de préproduction intermédiaires. Voici les étapes suivies dans cette section :

- Clonage de G1 et G2 vers LG1 et LG2
- Clonage de R1 et R2 vers LR1 et LR2
- Copie des fichiers de LG1 et LG2 dans LR1 et LR2
- (Facultatif) Personnalisation de LR1 et LR2
- Ajout du contenu de LR1 et LR2 à R1 et R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Cloner les référentiels G1 et G2 vers votre instance DSVM locale

Dans cette étape, vous clonez les référentiels ProjectTemplate (G1) et Utilities (G2) TDSP depuis les dépôts github TDSP vers les dossiers LG1 et LG2 dans votre instance DSVM locale :

- Créez un répertoire destiné à servir de répertoire racine pour l’hébergement de tous les clones des référentiels. 
    -  Dans l’instance DSVM Windows, créez le répertoire *C:\GitRepos\TDSPCommon*. 
    -  Dans l’instance DSVM Linux, créez le répertoire *GitRepos\TDSPCommon* dans votre répertoire de base. 

- Exécutez le jeu de commandes suivant à partir du répertoire *GitRepos\TDSPCommon*.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Ces scripts ont effectué les opérations suivantes (les dépôts apparaissent sous leurs noms abrégés) : 
    - Clonage de G1 vers LG1
    - Clonage de G2 vers LG2
- Une fois le clonage terminé, deux répertoires (_ProjectTemplate_ et _Utilities_), doivent apparaître sous le répertoire **GitRepos\TDSPCommon**. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Cloner les dépôts R1 et R2 vers votre instance DSVM locale

Dans cette étape, vous clonez les dépôts GroupProjectTemplate (R1) et GroupUtilities (R2) vers les répertoires locaux (appelés LR1 et LR2, respectivement) sous **GitRepos\GroupCommon** sur votre instance DSVM.

- Pour obtenir les URL des dépôts R1 et R2, accédez à votre page d’accueil **GroupCommon** sur VSTS. L’URL de cette page est généralement *https://\<nom de votre serveur VSTS\>.visualstudio.com/GroupCommon*. 
- Cliquez sur **CODE**. 
- Choisissez les dépôts **GroupProjectTemplate** et **GroupUtilities**. Copiez et enregistrez chacune des URL (HTTPS pour Windows ; SSH pour Linux) à partir de l’élément **URL clone**, tour à tour, en vue de les utiliser dans les scripts ci-après :  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Accédez au répertoire **GitRepos\GroupCommon** sur votre instance DSVM Windows ou Linux, puis exécutez un des ensembles de commandes suivants pour cloner R1 et R2 vers ce répertoire.
        
Voici les scripts de Windows et Linux :

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Des messages vous avertissant que LR1 et LR2 sont vides sont susceptibles de s’afficher.    

- Ces scripts ont effectué les opérations suivantes (les dépôts apparaissent sous leurs noms abrégés) : 
    - Clonage de R1 vers LR1
    - Clonage de R2 vers LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Amorcer GroupProjectTemplate (LR1) et GroupUtilities (LR2)

Ensuite, dans votre machine locale, copiez le contenu des répertoires ProjectTemplate et Utilities (à l’exception des métadonnées dans les répertoires .git) sous GitRepos\TDSPCommon vers vos répertoires GroupProjectTemplate et GroupUtilities sous **GitRepos\GroupCommon**. Voici les deux tâches qui composent cette étape :

- Copier les fichiers dans GitRepos\TDSPCommon\ProjectTemplate (**LG1**) vers GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Copier les fichiers dans GitRepos\TDSPCommon\Utilities (**LG2**) vers GitRepos\GroupCommon\Utilities (**LR2**) 

Pour effectuer ces deux tâches, exécutez les scripts suivants dans la console de PowerShell (Windows) ou dans la console de script Shell (Linux). Vous êtes invité à entrer les chemins complets de LG1, LR1, LG2 et LR2. Les chemins que vous entrez font l’objet d’une validation. Si vous entrez un répertoire qui n’existe pas, vous êtes invité à l’entrer de nouveau. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Comme vous pouvez le constater, les fichiers des répertoires LG1 et LG1 (sauf les fichiers du répertoire .git) ont été copiés vers LR1 et LR2, respectivement.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Comme vous pouvez le constater, les fichiers des dossiers (sauf les fichiers du répertoire .git) ont été copiés vers GroupProjectTemplate et GroupUtilities respectivement.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Ces scripts ont effectué les opérations suivantes (les dépôts apparaissent sous leurs noms abrégés) :
    - Copie des fichiers de LG1 dans LR1
    - Copie des fichiers de LG2 dans LR2

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Possibilité de personnaliser le contenu de LR1 et LR2
    
Si vous souhaitez personnaliser le contenu de LR1 et LR2 pour répondre aux besoins spécifiques de votre groupe, c’est à ce stade de la procédure qu’il convient de le faire. Vous pouvez modifier les documents de modèle, changer la structure de répertoire et ajouter des utilitaires existants que votre groupe a développés ou qui sont utiles pour l’ensemble de votre groupe. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Ajouter le contenu de LR1 et LR2 à R1 et R2 sur le serveur de groupe

Vous devez à présent ajouter le contenu de LR1 et LR2 aux dépôts R1 et R2. Voici les commandes git bash que vous pouvez exécuter dans Windows PowerShell ou Linux. 

Exécutez les commandes suivantes à partir du répertoire GitRepos\GroupCommon\GroupProjectTemplate :

    git status
    git add .
    git commit -m"push from DSVM"
    git push

L’option -m vous permet de définir un message pour votre validation git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Vous pouvez voir que dans le serveur VSTS de votre groupe, dans le dépôt GroupProjectTemplate, les fichiers sont synchronisés instantanément.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Enfin, accédez au répertoire **GitRepos\GroupCommon\GroupUtilities** et exécutez le même ensemble de commandes git bash :

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] S’il s’agit de votre première validation dans un dépôt Git, vous devez configurer les paramètres globaux *user.name* et *user.email* avant d’exécuter la commande `git commit`. Exécutez les deux commandes suivantes :
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Si la validation porte sur plusieurs dépôts Git, utilisez le même nom et la même adresse e-mail quand vous validez dans chacun d’eux. L’utilisation du même nom et de la même adresse e-mail s’avère plus pratique par la suite, au moment de créer des tableaux de bord PowerBI pour suivre vos activités Git sur plusieurs dépôts.


- Ces scripts ont effectué les opérations suivantes (les dépôts apparaissent sous leurs noms abrégés) :
    - Ajout du contenu de LR1 à R1
    - Ajout du contenu de LR2 à R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Ajouter des membres de groupe au serveur de groupe

À partir de la page d’accueil de votre serveur VSTS de groupe, cliquez sur **l’icône d’engrenage** en regard de votre nom d’utilisateur dans le coin supérieur droit, puis sélectionnez l’onglet **Sécurité**. Vous pouvez y ajouter des membres à votre groupe avec différentes autorisations.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Étapes suivantes

Les liens ci-dessous donnent accès à des descriptions plus détaillées des rôles et des tâches définis par le processus TDSP (Team Data Science Process) :

- [Tâches du responsable de groupe pour une équipe de science des données](group-manager-tasks.md)
- [Tâches du responsable d’une équipe de science des données](team-lead-tasks.md)
- [Tâches du coordinateur de projet pour une équipe de science des données](project-lead-tasks.md)
- [Contributeurs individuels de projet pour une équipe de science des données](project-ic-tasks.md)