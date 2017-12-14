---
title: "Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench | Microsoft Docs"
description: "Cet article explique comment utiliser un référentiel Git conjointement avec un projet Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: f4f1112fe68bdb2a26f68b3da08fe97f9d22d3b7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench
Découvrez comment Azure Machine Learning Workbench utilise Git pour gérer les versions et garantir la reproductibilité dans votre expérience de science des données. Découvrez comment associer votre projet à un référentiel Git cloud.

Machine Learning Workbench est conçu pour l’intégration Git. Lors de la création d'un projet, le dossier du projet est automatiquement « initialisé dans Git » en tant que référentiel Git local. Un second référentiel Git local, masqué, est créé avec une branche appelée AzureMLHistory/\<project GUID\>. La branche réalise le suivi des modifications des dossiers du projet lors de chaque exécution. 

L’association d’un projet Azure Machine Learning à un référentiel Git permet la gestion automatique de version, à la fois en local et à distance. Le référentiel Git est hébergé dans Visual Studio Team Services (Team Services). Dans la mesure où le projet Machine Learning est associé à un référentiel Git, tout utilisateur disposant d’un accès au référentiel à distance peut télécharger le code source le plus récent sur un autre ordinateur (itinérance).  

> [!NOTE]
> VSTS possède sa propre liste de contrôle d’accès, qui est indépendante du service Azure Machine Learning - Expérimentation. L’accès des utilisateurs peut varier entre un référentiel Git et un espace de travail ou un projet Machine Learning. Vous devrez éventuellement gérer l’accès. 
> 
> Que vous souhaitiez octroyer à un membre d’équipe un accès de niveau code à votre projet Machine Learning ou partager l’espace de travail, vous devez donner les autorisations appropriées aux utilisateurs devant accéder au référentiel Git Team Services. 

Pour gérer la gestion de version avec Git, vous pouvez utiliser la branche principale ou créer d’autres branches dans le référentiel. Vous pouvez également simplement utiliser le référentiel Git local et vous tourner vers le référentiel Git distant, si configuré.

Ce diagramme décrit la relation entre un référentiel Git Team Services et un projet Machine Learning :

![Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

Pour commencer à utiliser un référentiel Git distant, suivez la procédure décrite dans les sections suivantes.

> [!NOTE]
> Actuellement, Azure Machine Learning prend uniquement en charge les référentiels Git de comptes Team Services. La prise en charge des référentiels Git généraux, comme GitHub, est prévue pour l’avenir.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Étape 1. Créer un compte Machine Learning - Expérimentation
Créez un compte Expérimentation Machine Learning et installez l’application Azure Machine Learning Workbench. Pour plus d’informations, consultez le [guide de démarrage rapide d’installation et de création](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Étape 2. Créer un projet d’équipe ou utiliser un projet d’équipe existant
À partir du [portail Azure](https://portal.azure.com/), créez un projet d’équipe :
1. Sélectionnez **+**.
2. Recherchez **Projet d’équipe**.
3. Entrez les informations requises :
    - **Nom** : un nom d’équipe.
    - **Gestion de version** : sélectionnez **Git**.
    - **Abonnement** : sélectionnez un abonnement qui dispose d’un compte Expérimentation Machine Learning.
    - **Emplacement** : idéalement, restez dans une région proche de vos ressources Azure Machine Learning - Expérimentation.
4. Sélectionnez **Créer**. 

![Créer un projet d’équipe dans le portail Azure](media/using-git-ml-project/create_vsts_team.png)

Veillez à vous connecter à l’aide du compte Azure AD avec lequel vous accédez à Machine Learning Workbench. Si vous utilisez un autre compte, le système ne peut pas accéder à Machine Learning Workbench à l’aide de vos informations d’identification Azure AD. Une exception ? Si vous utilisez la ligne de commande pour créer le projet Machine Learning et fournissez un jeton d’accès personnel pour accéder au référentiel Git. Nous approfondirons ce point plus loin dans cet article.

Pour accéder directement au projet d’équipe créé, utilisez l’URL https://\<team project name\>.visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Étape 3. Configurer un projet Machine Learning et un référentiel Git

Pour configurer un projet Machine Learning, deux options s’offrent à vous :
- Créer un projet Machine Learning possédant un référentiel Git distant
- Associer un projet Machine Learning existant à un référentiel Git Team Services

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Créer un projet Machine Learning possédant un référentiel Git distant
Ouvrez Machine Learning Workbench et créez un projet. Dans la zone **Git repo (Dépôt Git)**, saisissez l’URL du référentiel Git Team Services de l’Étape 2. Elle se présente généralement ainsi : https://\<nom de compte Team Services\>.visualstudio.com/_git/\<nom du projet\>

![Créer un projet Machine Learning possédant un dépôt Git](media/using-git-ml-project/create_project_with_git_rep.png)

Il est également possible de créer le projet via l’outil de ligne de commande Azure (Azure CLI). Vous avez la possibilité de fournir un jeton d’accès personnel. Machine Learning peut utiliser ce jeton pour accéder au dépôt Git, au lieu d’utiliser vos informations d’identification Azure AD :

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> Si vous optez pour le modèle vierge de projet, le dépôt Git sélectionné peut déjà posséder une branche principale. Machine Learning clone simplement localement la branche principale. La solution ajoute le dossier aml_config et d’autres fichiers de métadonnées du projet au dossier local du projet. 
>
> Si vous choisissez tout autre modèle de projet, votre dépôt Git *ne peut pas* déjà posséder une branche principale. Si c’est le cas, une erreur s’affiche. L’autre solution consiste à utiliser la commande `az ml project create` pour créer le projet, avec un commutateur `--force`. Cette procédure supprime les fichiers de la branche principale d’origine et les remplace par les nouveaux fichiers dans le modèle que vous choisissez.

Un nouveau projet Machine Learning est maintenant créé avec l’intégration du référentiel Git distant activée. Le dossier du projet est toujours initialisé par Git comme un référentiel Git local. Comme le Git distant est défini sur le référentiel Git Team Services distant, les validations peuvent être transmises au référentiel Git distant.

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>Associer un projet Machine Learning existant à un référentiel Git Team Services
Vous pouvez créer un projet Machine Learning sans référentiel Git Team Services et vous appuyer sur le référentiel Git local pour obtenir des instantanés de l’historique des exécutions. Ultérieurement, vous pouvez associer un référentiel Git Team Services à ce projet Machine Learning existant à l’aide de la commande suivante :

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Vous pouvez exécuter l’opération update-repo uniquement sur un projet Machine Learning auquel aucun référentiel Git n’est associé. En outre, une fois que vous avez associé un référentiel Git à un projet Machine Learning, vous ne pouvez plus le supprimer.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Étape 4. Capturer un instantané de projet dans le référentiel Git
Exécutez quelques scripts dans le projet et apportez-y des modifications entre ces exécutions. Vous pouvez exécuter ces opérations dans l’application de bureau ou à partir de l’interface de ligne de commande Azure, à l’aide de la commande `az ml experiment submit`. Pour en savoir plus, consultez le [Didacticiel Classifying Iris](tutorial-classifying-iris-part-1.md) (Classification d’Iris). Pour chaque exécution, si des modifications sont effectuées dans le dossier du projet, un instantané de tout le dossier du projet est validé et placé dans le référentiel Git distant dans une branche principale appelée AzureMLHistory/\<project GUID\>. Pour afficher les branches et les validations, notamment la branche AzureMLHistory/\<project GUID\>, accédez à l’URL du référentiel Git Team Services. 

> [!NOTE] 
> La capture instantanée est validée uniquement avant l'exécution d'un script. Actuellement, l'exécution d'une préparation de données ou d'une cellule Notebook ne déclenche aucune capture instantanée.

![Branche de l’historique d’exécution](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Il est préférable de ne pas utiliser les commandes Git dans la branche de l'historique. Elles peuvent interférer avec l’historique d’exécutions. Utilisez plutôt la branche principale ou créez d'autres branches pour vos propres opérations Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Étape 5. Restaurer un instantané de projet précédent 
Pour restaurer le dossier complet du projet à l’état d’un instantané précédent de l’historique des exécutions, dans Machine Learning Workbench :
1. Dans la barre d’activités (icône de pointeur en sablier), sélectionnez **Exécutions**.
2. Dans la vue **Liste d’exécution**, sélectionnez l’exécution que vous souhaitez restaurer.
3. Dans la vue **Détails de l’exécution**, sélectionnez **Restaurer**.

![Branche de l’historique d’exécution](media/using-git-ml-project/restore_project.png)

Sinon, vous pouvez utiliser les commandes suivantes de la fenêtre de l’interface de ligne de commande Azure dans Machine Learning Workbench :

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Soyez prudent lorsque vous exécutez cette commande. Elle écrase l’intégralité du dossier du projet avec l’instantané pris lors du lancement de cette exécution spécifique. Votre projet est conservé dans la branche actuelle. Cela signifie que vous **perdez l’ensemble des modifications** apportées dans votre dossier de projet actuel.  

Vous avez intérêt à utiliser Git afin de valider vos modifications dans la branche actuelle avant d’effectuer cette opération.

## <a name="step-6-use-the-master-branch"></a>Étape 6. Utiliser la branche principale
Pour éviter de perdre accidentellement l’état de votre projet actuel, validez le projet dans la branche principale (ou toute branche que vous avez créée) du référentiel Git. Vous pouvez utiliser Git à partir de la ligne de commande (ou de votre outil client Git préféré) pour le faire fonctionner sur la branche principale. Par exemple :

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

Maintenant, vous pouvez restaurer en toute sécurité le projet vers un instantané antérieur, en exécutant l’Étape 5. Il est toujours possible de revenir à la validation apportée sur la branche principale.

## <a name="authentication"></a>Authentification
Si vous vous appuyez uniquement sur les fonctions d’historique des exécutions de Machine Learning pour prendre des instantanés du projet et les restaurer, vous n’avez pas à vous soucier de l’authentification du référentiel Git. L’authentification est gérée par la couche de service de Machine Learning - Expérimentation.

Toutefois, si vous utilisez vos propres outils Git pour gérer la version, vous devrez gérer l’authentification pour le référentiel Git distant sur Team Services. Dans Machine Learning, le référentiel Git distant est ajouté au référentiel local en tant qu’instance Git, à l’aide du protocole HTTPS. Cela signifie que lorsque vous envoyez des commandes Git au référentiel distant (commande push ou pull, par exemple), vous devez fournir un nom d'utilisateur et un mot de passe ou un jeton d'accès personnel. Pour créer un jeton d’accès personnel dans un référentiel Git Team Services, suivez les instructions de la section [Use a personal access token to authenticate](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) (S’authentifier avec un jeton d’accès personnel).

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [utiliser l’instance Team Data Science Process pour organiser votre structure de projet](how-to-use-tdsp-in-azure-ml.md).
