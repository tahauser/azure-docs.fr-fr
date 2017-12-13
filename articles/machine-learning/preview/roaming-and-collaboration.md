---
title: "Itinérance et collaboration dans Azure Machine Learning Workbench | Microsoft Docs"
description: "Découvrez comment configurer l’itinérance et la collaboration dans Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 137608007716452ec6468f1e13f494b095a11cb0
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Itinérance et collaboration dans Azure Machine Learning Workbench
Cet article vous explique comment utiliser Azure Machine Learning Workbench pour configurer des projets pour l’itinérance entre les ordinateurs et collaborer avec les membres d’équipe. 

Lorsque vous créez un projet Azure Machine Learning avec un lien vers un référentiel Git distant, les instantanés et les métadonnées du projet sont stockés dans le cloud. Le lien cloud vous permet d’accéder au projet depuis un autre ordinateur (itinérance). Vous pouvez également collaborer avec des membres d’équipe en leur octroyant un accès au projet. 

## <a name="prerequisites"></a>Composants requis
1. Installez l’application Machine Learning Workbench. Assurez-vous de disposer d’un accès à un compte Azure Machine Learning - Expérimentation. Pour plus d’informations, consultez le [Guide d’installation](quickstart-installation.md).

2. Accédez à [Visual Studio Team Services](https://www.visualstudio.com) (Team Services), puis créez un référentiel auquel lier votre projet. Pour plus d’informations, consultez la section [Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Créer un projet Machine Learning
Ouvrez Machine Learning Workbench, puis créez un projet (par exemple, un projet nommé iris). Dans le champ **URL du dépôt GIT Visualstudio.com**, saisissez une URL valide pour un référentiel Git Team Services. 

> [!IMPORTANT]
> Si vous optez pour le modèle vierge de projet, le dépôt Git sélectionné peut déjà posséder une branche principale. Machine Learning clone simplement localement la branche principale. La solution ajoute le dossier aml_config et d’autres fichiers de métadonnées du projet au dossier local du projet. 
>
> Si vous choisissez tout autre modèle de projet, votre dépôt Git *ne peut pas* déjà posséder une branche principale. Si c’est le cas, une erreur s’affiche. L’autre solution consiste à utiliser la commande `az ml project create` pour créer le projet, avec un commutateur `--force`. Cette procédure supprime les fichiers de la branche principale d’origine et les remplace par les nouveaux fichiers dans le modèle que vous choisissez.

Une fois le projet créé, exécutez quelques scripts au sein du projet. Cette action valide l’état du projet dans la branche de l’historique des exécutions du référentiel Git distant. 

> [!NOTE] 
> Seules les exécutions de script déclenchent des validations sur la branche de l’historique des exécutions. L’exécution de préparation des données ou les exécutions de Notebook ne déclenchent pas de captures instantanées de projet sur la branche de l’historique des exécutions.

Si vous avez configuré l’authentification Git, vous pouvez également travailler dans la branche principale. Vous avez également la possibilité de créer une nouvelle branche. 

Exemple : 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Itinérance
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Ouvrir Machine Learning Workbench sur un second ordinateur
Une fois le référentiel Git Team Services lié à votre projet, vous pouvez accéder au projet iris depuis n’importe quel ordinateur sur lequel vous avez installé Azure Machine Learning Workbench. 

Pour accéder au projet iris sur un autre ordinateur, vous devez vous connecter à l’application avec les mêmes informations d’identification utilisées lors de la création du projet. Vous devez également vous trouver dans les mêmes compte et espace de travail Machine Learning - Expérimentation. Le projet iris est répertorié par ordre alphabétique avec les autres projets dans l’espace de travail. 

### <a name="download-the-project-on-a-second-computer"></a>Télécharger le projet sur un second ordinateur
Lorsque l’espace de travail est ouvert sur le second ordinateur, l’icône située en regard du projet iris diffère de l’icône de dossier par défaut. L’icône de téléchargement indique que le contenu du projet se trouve dans le cloud et doit être téléchargé sur l’ordinateur actuel. 

![Créer un projet](./media/roaming-and-collaboration/downloadable-project.png)

Sélectionnez le projet iris pour amorcer un téléchargement. Une fois le téléchargement terminé, le projet est accessible sur le second ordinateur. 

Sous Windows, le projet se trouve à l’emplacement C:\Users\\<username\>\Documents\AzureML.

Sous macOS, le projet se trouve à l’emplacement /home/\<username\>/Documents/AzureML.

Dans une prochaine version, nous prévoyons d’améliorer les fonctionnalités pour vous permettre de sélectionner un dossier de destination. 

> [!NOTE]
> Si un dossier dans le répertoire Machine Learning a exactement le même nom que le projet, le téléchargement échoue. Pour contourner ce problème, renommez temporairement le dossier existant.


### <a name="work-on-the-downloaded-project"></a>Travailler sur le projet téléchargé 
Le projet qui vient d’être téléchargé reflète l’état du projet depuis la dernière exécution du projet. Un instantané de l’état du projet est automatiquement validé sur la branche de l’historique des exécutions dans le référentiel Git Team Services à chaque exécution du projet. Nous utilisons l’instantané associé à la dernière exécution lors de l’instanciation du projet sur le deuxième ordinateur. 
 

## <a name="collaboration"></a>Collaboration
Vous pouvez collaborer avec des membres d’équipe sur des projets liés au référentiel Git Team Services. Vous pouvez affecter des autorisations aux utilisateurs pour le compte, l’espace de travail et le projet Machine Learning - Expérimentation. Actuellement, vous pouvez exécuter des commandes Azure Resource Manager à l’aide de l’interface de ligne de commande Azure. Vous pouvez également utiliser le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez la section [Ajout d’utilisateurs depuis le portail Azure](#portal)    

### <a name="use-the-command-line-to-add-users"></a>Ajout d’utilisateurs depuis la ligne de commande
Par exemple, disons qu’Alice est propriétaire du projet iris. Alice souhaite partager l’accès au projet avec Bob. 

Alice sélectionne le menu **Fichier**, puis l’élément du menu **Invite de commandes**. La fenêtre d’invite de commandes s’ouvre avec le projet iris. Alice peut alors décider du niveau d’accès qu’elle souhaite octroyer à Bob. Elle accorde des autorisations en exécutant les commandes suivantes :  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

Après l’attribution du rôle, directement ou par héritage, Bob peut voir le projet dans la liste de projets Machine Learning Workbench. Pour accéder au projet, il est possible que Bob doive redémarrer l’application. Il peut ensuite télécharger le projet tel que décrit dans [Itinérance](#roaming), puis commencer à collaborer avec Alice. 

L’historique des exécutions de tous les utilisateurs qui collaborent sur un projet est validé dans le même référentiel Git distant. Quand Alice exécute le projet, Bob peut voir cette exécution dans la section de l’historique des exécutions du projet dans l’application Machine Learning Workbench. Bob peut également restaurer le projet à l’état de n’importe quelle exécution, y compris celles lancées par Alice. 

Le partage d’un référentiel Git distant pour le projet permet également à Alice et Bob de collaborer sur la branche principale. Si nécessaire, ils peuvent aussi créer des branches personnelles et utiliser des demandes de tirage (pull requests) et des fusions Git pour collaborer. 

### <a name="use-the-azure-portal-to-add-users"></a>Ajout d’utilisateurs depuis le portail Azure
<a name="portal"></a>

Les comptes, espaces de noms et projets Machine Learning - Expérimentation sont des ressources Azure Resource Manager. Vous pouvez utiliser le lien de **contrôle d’accès** du [portail Azure](https://portal.azure.com) pour attribuer des rôles. 

Trouvez la ressource à laquelle ajouter les utilisateurs à partir de la vue **Toutes les ressources**. Sélectionnez le lien **Contrôle d’accès (IAM)**, puis sélectionnez **Ajouter des utilisateurs**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Exemple de flux de travail de collaboration
Pour illustrer le flux de travail de collaboration, étudions un exemple. Les employés de Contoso, Alice et Bob, veulent collaborer sur un projet de science des données à l’aide de Machine Learning Workbench. Leur identité appartiennent au même locataire Contoso Azure Active Directory (Azure AD). Voici la procédure suivie par Alice et Bob :

1. Alice crée un référentiel Git vide dans un projet Team Services. Le projet Team Services doit se trouver dans un abonnement Azure créé sous le locataire Contoso Azure AD. 

2. Alice crée un compte Machine Learning - Expérimentation, un espace de travail et un projet Machine Learning Workbench sur son ordinateur. Lorsqu’elle crée le projet, elle saisit l’URL du référentiel Git Team Services.

3. Alice commence à travailler sur le projet. Elle crée des scripts et les exécute plusieurs fois. Pour chaque exécution, une capture instantanée de l’ensemble du dossier de projet est automatiquement placée dans une branche de l’historique des exécutions du référentiel Git Team Services créé par Machine Learning Workbench.

4. Alice est satisfaite du travail en cours. Elle souhaite valider ses modifications dans la branche principale locale et les placer dans la branche principale du référentiel Git Team Services. Avec le projet ouvert, dans Machine Learning Workbench, elle ouvre la fenêtre Invite de commandes, puis saisit ces commandes :
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alice ajoute ensuite Bob en tant que contributeur à l’espace de travail. Elle peut le faire dans le portail Azure ou à l’aide de la commande `az role assignment`, tel qu’illustré plus tôt. Alice octroie également à Bob des autorisations de lecture/écriture dans le référentiel Git Team Services.

6. Bob se connecte à Machine Learning Workbench sur son ordinateur. Il peut voir l’espace de travail qu’Alice a partagé avec lui. Il a accès au projet iris répertorié sous cet espace de travail. 

7. Bob sélectionne le nom du projet. Le projet est téléchargé sur son ordinateur.
    * Les fichiers de projet téléchargé sont des copies de la capture instantanée de la dernière exécution enregistrée dans l’historique des exécutions. Ils ne correspondent pas à la dernière validation sur la branche principale.
    * Le dossier de projet local est défini sur la branche principale, avec les modifications non indexées.

8. Bob peut consulter les exécutions menées à bien par Alice. Il peut restaurer des instantanés d’exécutions antérieures.

9. Bob veut obtenir les dernières modifications portées par Alice, puis commencer à travailler dans une branche différente. Dans Machine Learning Workbench, il ouvre une fenêtre Invite de commandes, puis exécute les commandes suivantes :

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob modifie le projet et soumet de nouvelles exécutions. Les modifications sont apportées sur le branche bob. Les exécutions de Bob sont également visibles par Alice.

11. Bob peut maintenant placer ses modifications sur le référentiel Git distant. Afin d’éviter tout conflit avec la branche principale dans laquelle Alice travaille, il décide de placer son travail dans une nouvelle branche distante également nommée bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Pour informer Alice de la nouvelle astuce présente dans son code, Bob crée une demande de tirage (pull request) sur le référentiel Git distant de la branche bob vers la branche principale. Alice peut alors fusionner la demande de tirage (pull request) dans la branche principale.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations, consultez la section [Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench](using-git-ml-project.md).
