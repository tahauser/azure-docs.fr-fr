---
title: "Itinérance et collaboration dans Azure Machine Learning Workbench  | Microsoft Docs"
description: "Liste des problèmes connus et guide de dépannage"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 50f48fb096cb907e050769a8a4159689eb25418c
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Itinérance et collaboration dans Azure Machine Learning Workbench
Ce document vous explique en détail comment Azure Machine Learning Workbench facilite l’itinérance de vos projets entre différents ordinateurs ainsi que la collaboration avec vos coéquipiers. 

Lorsque vous créez un projet Azure Machine Learning avec un lien vers un référentiel Git distant, les instantanés et les métadonnées du projet sont stockés dans le cloud. Le lien cloud vous permet d’accéder au projet depuis un autre ordinateur (itinérance). Vous pouvez également accorder un accès à vos collègues, ce qui permet la collaboration. 

## <a name="prerequisites"></a>Composants requis
Installez d’abord Azure Machine Learning Workbench avec un accès à un compte Experimentation. Consultez le [guide d’installation](quickstart-installation.md) pour plus de détails.

Accédez ensuite à [Visual Studio Team System](https://www.visualstudio.com) et créez un référentiel auquel lier votre projet. Pour obtenir des informations détaillées sur Git, consultez l’article [Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-azure-machine-learning-project"></a>Créer un projet Azure Machine Learning
Lancez Azure Machine Learning Workbench et créez un projet (par exemple, _iris_). Saisissez l’URL du référentiel Git VSTS dans la zone de texte **URL du référentiel GIT Visualstudio.com**. 

> [!IMPORTANT]
> Vous pouvez opter pour le modèle de projet vide à condition que le référentiel Git choisi possède déjà une branche _principale_. Azure ML clone simplement la branche _principale_ localement puis ajoute le dossier `aml_config` et les autres fichiers de métadonnées du projet au dossier du projet local. Mais si vous choisissez un autre modèle de projet, votre référentiel Git ne doit pas contenir de branche _principale_. Sinon, une erreur s’affiche. L’autre solution consiste à utiliser l’outil de ligne de commande `az ml project create` pour créer le projet et à fournir un commutateur `--force`. Cette procédure supprime les fichiers de la branche principale d’origine et les remplace par les nouveaux fichiers dans le modèle que vous choisissez.

Une fois le projet créé, exécutez quelques scripts au sein du projet. Cette action valide l’état du projet dans la branche de l’historique des exécutions du référentiel Git distant. 

> [!NOTE] 
> Seules les exécutions de script déclenchent des validations sur la branche de l’historique des exécutions. L’exécution de préparation des données ou les exécutions de Notebook ne déclenchent pas de captures instantanées de projet sur la branche de l’historique des exécutions.

Si vous avez configuré l’authentification Git, vous pouvez également explicitement travailler dans la branche principale ou créer une branche. 

Par exemple : 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Itinérance
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Ouvrir Azure Machine Learning Workbench sur un second ordinateur
Une fois le référentiel Git VSTS lié à votre projet, vous pouvez accéder au projet _iris_ depuis n’importe quel ordinateur sur lequel vous avez installé Azure Machine Learning Workbench. 

Pour accéder au projet iris sur un autre ordinateur, vous devez vous connecter à l’application avec les mêmes informations d’identification utilisées lors de la création du projet. En outre, vous devez accéder au même compte Experimentation et au même espace de travail. Le projet _iris_ est répertorié par ordre alphabétique avec les autres projets dans l’espace de travail. 

### <a name="download-project-on-second-machine"></a>Télécharger le projet sur le deuxième ordinateur
Lorsque l’espace de travail est ouvert sur le deuxième ordinateur, l’icône située en regard du projet _iris_ diffère de l’icône de dossier par défaut. L’icône de téléchargement indique que le contenu du projet se trouve dans le cloud et doit être téléchargé sur l’ordinateur actuel. 

![créer un projet](./media/roaming-and-collaboration/downloadable-project.png)

Lorsque vous cliquez sur le projet _iris_, vous lancez un téléchargement. Après quelques instants, une fois le téléchargement terminé, le projet est accessible sur le deuxième ordinateur. 

Emplacement sous Windows : `C:\Users\<username>\Documents\AzureML`

Emplacement sous macOS : `/home/<username>/Documents/AzureML`

Dans une prochaine version, nous prévoyons d’améliorer les fonctionnalités pour vous permettre de sélectionner un dossier de destination. 

> [!NOTE]
> Si un dossier dans le répertoire Azure ML a exactement le même nom que le projet, le téléchargement échoue. À l’heure actuelle, vous devez renommer le dossier existant afin de contourner ce problème.


### <a name="work-on-the-downloaded-project"></a>Travailler sur le projet téléchargé 
Le projet qui vient d’être téléchargé reflète l’état du projet depuis la dernière exécution du projet. Un instantané de l’état du projet est automatiquement validé sur la branche de l’historique des exécutions dans le référentiel Git VSTS à chaque exécution du projet. Nous utilisons l’instantané associé à la dernière exécution lors de l’instanciation du projet sur le deuxième ordinateur. 
 

## <a name="collaboration"></a>Collaboration
Vous pouvez collaborer avec vos coéquipiers sur des projets liés à un référentiel Git VSTS. Vous pouvez affecter des autorisations aux utilisateurs sur le compte Experimentation, l’espace de travail et le projet. À ce stade, vous pouvez exécuter les commandes Azure Resource Manager à l’aide de l’interface de ligne de commande Azure. Vous pouvez également utiliser le [portail Azure](https://portal.azure.com). Voir la [section suivante](#portal).    

### <a name="using-command-line-to-add-users"></a>Utilisation de la ligne de commande pour ajouter des utilisateurs
Prenons un exemple. Par exemple, Alice est la propriétaire du projet Iris et souhaite en partager l’accès avec Bob. 

Alice clique sur le menu **Fichier** puis sélectionne l’option **Invite de commande** pour ouvrir l’invite de commande configurée pour le projet _iris_. Alice peut alors choisir le niveau accès à accorder à Bob en exécutant les commandes suivantes.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

Après l’attribution du rôle, directement ou par héritage, Bob peut voir le projet dans la liste des projets Workbench. L’application peut nécessiter un redémarrage pour afficher le projet. Bob peut ensuite télécharger le projet comme décrit dans la section [Itinérance](#roaming) et collaborer avec Alice. 

L’historique des exécutions de tous les utilisateurs qui collaborent sur un projet est validé dans le même référentiel Git distant. Par conséquent, quand Alice exécute le projet, Bob peut voir cette exécution dans la section de l’historique des exécutions du projet dans l’application Workbench. Bob peut également restaurer le projet à l’état de n’importe quelle exécution, y compris celles lancées par Alice. 

Le partage d’un référentiel Git distant pour le projet permet également à Alice et Bob de collaborer sur la branche principale. Si nécessaire, ils peuvent aussi créer des branches personnelles et utiliser des demandes de tirage (pull requests) et des fusions Git pour collaborer. 

### <a name="using-azure-portal-to-add-users"></a>Utilisation du portail Azure pour ajouter des utilisateurs
<a name="portal"></a>

Les comptes, espaces de travail et projets Azure Machine Learning Experimentation sont des ressources Azure Resource Manager. Vous pouvez utiliser le lien de contrôle d’accès du [portail Azure](https://portal.azure.com) pour attribuer des rôles. 

Trouvez la ressource à ajouter aux utilisateurs à partir de la vue Toutes les ressources. Cliquez sur le lien de contrôle d’accès (IAM) dans la page. Ajouter des utilisateurs 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Exemple de flux de travail de collaboration
Pour illustrer le flux de collaboration, étudions un exemple. Les employés de Contoso, Alice et Bob, veulent collaborer sur un projet de science des données à l’aide d’Azure ML Workbench. Leurs identités appartiennent au même locataire Contoso Azure AD.

1. Alice crée tout d’abord un référentiel Git vide dans un projet VSTS. Ce projet VSTS doit se trouver dans un abonnement Azure créé sous le locataire Contoso AAD. 

2. Alice crée ensuite un compte d’expérimentation Azure ML, un espace de travail et un projet Azure ML Workbench sur son ordinateur. Elle fournit l’URL de référentiel Git lors de la création du projet.

3. Alice commence à travailler sur le projet. Elle crée des scripts et les exécute plusieurs fois. Pour chaque exécution, une capture instantanée de l’ensemble du dossier de projet est automatiquement placée dans une branche de l’historique des exécutions du référentiel Git VSTS créé par Workbench en tant qu’une validation.

4. Alice est désormais satisfaite du travail en cours. Elle souhaite valider sa modification dans la branche _master_ locale et la place dans la branche _master_ du référentiel Git VSTS. Pour cela, avec le projet, elle ouvre la fenêtre d’invite de commandes à partir d’Azure ML Workbench et exécute les commandes suivantes :
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. Alice ajoute ensuite Bob à l’espace de travail en tant que contributeur. Pour cela, elle utilise le portail Azure ou la commande `az role assignment` illustrée ci-dessus. Elle accorde également à Bob un accès en lecture/écriture sur le référentiel Git VSTS.

6. Bob se connecte alors à Azure ML Workbench sur son ordinateur. Il peut voir l’espace de travail qu’Alice a partagé avec lui, ainsi que le projet répertorié sous cet espace de travail. 

7. Bob clique sur le nom du projet, et le projet est téléchargé sur son ordinateur.
    
    a. Les fichiers de projet téléchargé sont des clones de la capture instantanée de la dernière exécution enregistrée dans l’historique des exécutions. Ils ne correspondent pas à la dernière validation sur la branche principale.
    
    b. Le dossier de projet local est défini sur la branche _master_ avec les modifications non indexées.

8. Bob peut désormais rechercher les exécutions réalisées par Alice et restaurer la capturer instantanée d’exécutions précédentes.

9. Bob souhaite obtenir les dernières modifications apportées par Alice et commencer à travailler sur une autre branche. Pour cela, il ouvre la fenêtre d’invite de commandes à partir d’Azure ML Workbench et exécute les commandes suivantes :

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. Bob modifie maintenant le projet et soumet de nouvelles exécutions. Les modifications sont apportées sur le branche _bob_. Et Alice peut également voir les exécutions de Bob.

11. Bob peut maintenant placer ses modifications dans le référentiel Git distant. Afin d’éviter tout conflit avec la branche _master_ dans laquelle Alice travaille, il décide de placer son travail dans une nouvelle branche distante également nommée _bob_.

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. Bob peut alors informer Alice de la nouvelle astuce présente dans son code et crée une demande de tirage (pull request) sur le référentiel Git distant de la branche _bob_ vers le branche _master_. Alice peut alors fusionner la demande de tirage (pull request) dans la branche _master_.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’utilisation de Git avec Azure ML Workbench : [Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench](using-git-ml-project.md)