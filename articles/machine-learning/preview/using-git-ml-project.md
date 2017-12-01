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
ms.openlocfilehash: 0cd447a52964578dd2348a786dd57a45ea87516e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Utilisation d’un référentiel Git avec un projet Azure Machine Learning Workbench
Ce document fournit des informations sur la façon dont Azure Machine Learning Workbench utilise Git pour gérer les versions et garantir la reproductibilité dans votre expérience de science des données. Des instructions sur la façon d’associer votre projet à un référentiel Git cloud sont également fournies.

## <a name="introduction"></a>Introduction
Azure Machine Learning Workbench a été conçu d’emblée pour l’intégration de Git. Lors de la création d'un projet, le dossier du projet est automatiquement « initialisé dans Git » en tant que référentiel Git local (repo). Entre-temps, un second référentiel Git local masqué est également créé avec une branche nommée _AzureMLHistory/<GUID_projet>_ pour garder la trace des changements apportés au dossier du projet à chaque exécution. 

L’association du projet Azure ML avec un référentiel Git, hébergé dans un projet Visual Studio Team Services (VSTS), permet une gestion de version automatique à la fois localement et à distance. Cette association permet à toute personne ayant accès au référentiel distant de télécharger le dernier code source vers un autre ordinateur (itinérance).  

> [!NOTE]
> VSTS possède sa propre liste de contrôle d’accès, qui est indépendante du service Azure Machine Learning Experimentation. L’accès de l’utilisateur peut varier entre un référentiel Git et un espace de travail ou un projet Azure ML, et devra éventuellement être géré. Si vous souhaitez partager votre projet Azure ML avec un membre de l’équipe, y compris l’accès au niveau du code, en plus de partager uniquement l’espace de travail, vous devez lui accorder explicitement des droits d’accès au référentiel Git VSTS. 

Avec Git, il est également possible de gérer la version explicitement à l’aide de la branche _principale_ ou en en créant d’autres branches sur le référentiel. Vous pouvez simplement utiliser le référentiel Git local vous tourner vers le référentiel Git distant (si configuré).

Ce diagramme illustre la relation entre un référentiel Git VSTS et un projet Azure ML :

![AML Git](media/using-git-ml-project/aml_git.png)

Pour commencer à utiliser un référentiel Git distant, suivez ces instructions de base.

> [!NOTE]
> Actuellement, Azure Machine Learning prend uniquement en charge les référentiels Git de comptes VSTS. Une prise en charge des référentiels Git généraux (par exemple, GitHub) est prévue prochainement.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>Étape 1. Créer un compte Azure ML Experimentation
Si ce n’est pas déjà fait, créez un compte Azure ML Experimentation et installez l’application Azure ML Workbench. Pour plus de détails, consultez le document [Guide de démarrage rapide d’installation et de création](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Étape 2. Créer un projet d’équipe ou utiliser un projet d’équipe existant
À partir du [portail Azure](https://portal.azure.com/), créez un **projet d’équipe**.
1. Cliquez sur **+**
2. Recherchez **« Projet d’équipe »**
3. Entrez les informations requises.
    - Nom : un nom d’équipe.
    - Gestion de version : **Git**
    - Abonnement : un abonnement incluant un compte Azure Machine Learning Experimentation.
    - Emplacement : idéalement, restez dans une région proche de vos ressources Azure Machine Learning Experimentation.
4. Cliquez sur **Créer**. 

![Créer un projet d’équipe à partir du portail Azure](media/using-git-ml-project/create_vsts_team.png)

Veillez à vous connecter avec le même compte Azure Active Directory (AAD) utilisé pour accéder à Azure Machine Learning Workbench. Sinon, le système ne peut pas y accéder à l'aide de vos informations d'identification AAD, à moins d'utiliser la ligne de commande pour créer le projet Azure ML et de fournir un jeton d'accès personnel pour accéder au référentiel Git. Des informations complémentaires vous seront fournies ultérieurement.

Une fois le projet d’équipe créé, vous êtes prêt à passer à l’étape suivante.

Pour accéder directement au projet d’équipe que vous venez de créer, l’URL est `https://<team_project_name>.visualstudio.com`.

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Étape 3. Créer un projet Azure ML avec un référentiel Git distant
Lancez Azure ML Workbench et créez un projet. Entrez dans la zone de texte du référentiel Git l’URL du référentiel Git VSTS que vous obtenez à l’étape 2. Elle se présente généralement sous cette forme : `http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Créer un projet Azure ML avec le référentiel Git](media/using-git-ml-project/create_project_with_git_rep.png)

Vous pouvez également créer le projet à l'aide de l'outil de ligne de commande. Vous avez la possibilité de fournir un jeton d'accès personnel. Azure ML peut utiliser ce jeton pour accéder au référentiel Git en votre nom, au lieu de se fier à vos informations d'identification AAD :

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> Vous pouvez opter pour le modèle de projet vide à condition que le référentiel Git choisi possède déjà une branche _principale_. Azure ML clone simplement la branche _principale_ localement puis ajoute le dossier `aml_config` et les autres fichiers de métadonnées du projet au dossier du projet local. Mais si vous choisissez un autre modèle de projet, votre référentiel Git ne doit pas contenir de branche _principale_. Sinon, une erreur s'affiche. L'autre solution consiste à utiliser l'outil de ligne de commande `az ml project create` pour créer le projet et à fournir un commutateur `--force`. Cette procédure supprime les fichiers de la branche principale d'origine et les remplace par les nouveaux fichiers dans le modèle que vous choisissez.

Un nouveau projet Azure ML est maintenant créé avec l’intégration du référentiel Git distant activée et fonctionnelle. Le dossier du projet est toujours initialisé par Git comme un référentiel Git local. Et comme le Git _distant_ est défini sur le référentiel Git VSTS distant, les validations peuvent être transmises au référentiel Git distant.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Étape 3a. Associer un projet Azure ML existant avec un référentiel Git VSTS
Vous pouvez également créer un projet Azure ML sans référentiel Git VSTS et vous appuyer uniquement sur le référentiel Git local pour obtenir des instantanés de l’historique des exécutions. Et vous pouvez associer un référentiel Git VSTS ultérieurement à ce projet Azure ML existant à l’aide de la commande suivante :

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> Vous pouvez uniquement effectuer l'opération update-repo sur un projet Azure ML auquel aucun référentiel Git n'est associé. Et une fois le référentiel Git associé, il ne peut pas être supprimé.

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Étape 4. Capturer un instantané de projet dans le référentiel Git
Vous pouvez maintenant exécuter quelques scripts dans le projet et y apporter des modifications entre ces exécutions. Vous pouvez le faire à partir de l’application de bureau ou à l’aide de la commande `az ml experiment submit` dans l’interface CLI. Pour plus d’informations, vous pouvez suivre le [Didacticiel Classifying Iris (Classification d’Iris)](tutorial-classifying-iris-part-1.md). Pour chaque exécution, si des modifications sont effectuées dans des fichiers du dossier du projet, un instantané de tout le dossier du projet est validé et placé dans le référentiel Git distant dans une branche principale appelée `AzureMLHistory/<Project_GUID>`. Vous pouvez afficher les branches et les validations en accédant à l’URL de référentiel Git VSTS puis en recherchant cette branche. 

> [!NOTE] 
> La capture instantanée est validée uniquement avant l'exécution d'un script. Actuellement, l'exécution d'une préparation de données ou d'une cellule Notebook ne déclenche aucune capture instantanée.

![branche de l’historique d’exécution](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Il est préférable de ne pas utiliser les commandes Git dans la branche de l'historique. Vous risqueriez de détériorer l'historique d'exécution. Utilisez la branche principale ou créez d'autres branches pour vos propres opérations Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Étape 5. Restaurer un instantané de projet précédent 
Pour restaurer le dossier complet du projet à l’état d’un instantané précédent de l’historique des exécutions, à partir d’Azure ML Workbench :
1. Cliquez sur **Exécutions** dans la barre d’activité (icône sablier).
2. Dans la vue **Liste d’exécution**, cliquez sur l’exécution que vous souhaitez restaurer.
3. Dans la vue **Détails de l’exécution**, cliquez sur **Restaurer**.

![branche de l’historique d’exécution](media/using-git-ml-project/restore_project.png)

Vous pouvez également utiliser la commande suivante à partir de la fenêtre de l’interface CLI Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

En exécutant cette commande, nous remplaçons le dossier complet du projet par l’instantané pris lors du lancement de cette exécution spécifique. Mais votre projet reste sur la branche actuelle. Cela signifie que vous allez **perdre toutes les modifications** apportées dans votre dossier de projet actuel. Par conséquent, soyez très prudent lorsque vous exécutez cette commande. Vous pourriez configurer Git pour valider vos modifications dans la branche actuelle avant d'effectuer l'opération ci-dessus. Voir les sections précédentes pour plus de détails.

## <a name="step-6-use-the-master-branch"></a>Étape 6. Utiliser la branche principale
Pour éviter de perdre accidentellement l’état de votre projet actuel, validez le projet dans la branche principale (ou toute branche que vous avez créée) du référentiel Git. Vous pouvez directement utiliser Git à partir de la ligne de commande (ou de votre outil client Git préféré) pour le faire fonctionner sur la branche principale. Par exemple :

```sh
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

Vous pouvez maintenant restaurer sans risque le projet vers un instantané antérieur à partir de l’étape 5, en sachant que vous pouvez toujours revenir à la validation que vous venez d’effectuer sur la branche principale.

## <a name="authentication"></a>Authentification
Si vous utilisez uniquement des fonctions d’historique des exécutions dans Azure ML pour prendre des instantanés du projet et les restaurer, vous n’avez pas à vous soucier de l’authentification du référentiel Git. La couche Service d’expérimentation s’en occupe.

Toutefois, si vous utilisez vos propres outils Git pour gérer la version, vous devrez gérer correctement l’authentification pour le référentiel Git distant sur VSTS. Dans Azure ML, le référentiel Git distant est ajouté au référentiel local en tant que Git distant à l'aide du protocole HTTPS. Cela signifie que lorsque vous envoyez des commandes Git au référentiel distant (commande push ou pull, par exemple), vous devez fournir un nom d'utilisateur et un mot de passe ou un jeton d'accès personnel. Suivez [ces instructions](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) pour créer un jeton d'accès personnel dans un référentiel Git VSTS.

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment utiliser le processus Team Data Science Process (TDSP) afin d’organiser votre structure de projet, consultez [Structurer un projet avec TDSP](how-to-use-tdsp-in-azure-ml.md)
