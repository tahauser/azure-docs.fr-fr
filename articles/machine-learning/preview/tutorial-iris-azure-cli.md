---
title: "Article du didacticiel sur les fonctionnalités Azure Machine Learning (préversion) - Interface de ligne de commande | Microsoft Docs"
description: "Ce didacticiel décrit toutes les étapes nécessaires pour réaliser une classification Iris de bout en bout à partir de l’interface de ligne de commande."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: ad81cd02ba0c46cbe58de7071d2164aaefea6514
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Didacticiel : Classification Iris à l’aide de l’interface de ligne de commande
Les services Azure Machine Learning (préversion) forment une solution d’analytique avancée et de science des données intégrée de bout en bout qui permet aux scientifiques des données professionnels de préparer des données, développer des expériences et déployer des modèles à l’échelle du cloud.

Dans ce didacticiel, vous allez apprendre à utiliser les outils de l’interface de ligne de commande (CLI) dans les fonctionnalités Azure Machine Learning en préversion pour : 
> [!div class="checklist"]
> * Configurer un compte d’expérimentation et créer un espace de travail
> * Création d’un projet
> * Soumettre une expérimentation à plusieurs cibles de calcul
> * Promouvoir et inscrire un modèle formé
> * Déployer un service web pour évaluer de nouvelles données

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous avez besoin des éléments suivants :
- Un accès à un abonnement Azure et des autorisations adéquates pour créer des ressources dans cet abonnement. 
  
  Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Une application Azure Machine Learning Workbench installée comme décrit dans [Créer des comptes en préversion Azure Machine Learning et installer Azure Machine Learning Workbench](quickstart-installation.md). 

  >[!IMPORTANT]
  >Ne créez pas les comptes de service Azure Machine Learning, car vous le ferez à l’aide de l’interface de ligne de commande dans cet article.
 
## <a name="getting-started"></a>Prise en main
L’interface de ligne de commande (CLI) Azure Machine Learning vous permet d’effectuer toutes les tâches nécessaires à un flux de travail de science des données complet. Vous pouvez accéder à des outils CLI des manières suivantes :

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Option 1. Lancer l’interface de ligne de commande Azure ML à partir de la boîte de dialogue de connexion Azure Machine Learning Workbench
Quand vous lancez Azure Machine Learning Workbench et vous connectez pour la première fois, l’écran suivant s’affiche si vous n’avez pas encore accès à un compte d’expérimentation :

![aucun compte trouvé](media/tutorial-iris-azure-cli/no_account_found.png)

Cliquez sur le lien **Fenêtre de ligne de commande** dans la boîte de dialogue pour ouvrir la fenêtre de ligne de commande.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Option 2. Lancer l’interface de ligne de commande Azure ML à partir de l’application Azure Machine Learning Workbench
Si vous avez déjà accès à un compte d’expérimentation, vous pouvez vous y connecter. Ouvrez ensuite la fenêtre de ligne de commande en cliquant sur le menu **Fichier** --> **Ouvrir l’invite de commandes**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Option 3. Activer l’interface de ligne de commande Azure ML dans une fenêtre de ligne de commande de votre choix
Vous pouvez également activer l’interface de ligne de commande Azure ML dans n’importe quelle fenêtre de ligne de commande. Pour ce faire, ouvrez une fenêtre de commande et entrez les commandes suivantes :

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Pour rendre la modification permanente, vous pouvez utiliser `SETX` sur Windows. Pour macOS, vous pouvez utiliser `setenv`.

>[!TIP]
>Vous pouvez activer l’interface de ligne de commande Azure dans votre fenêtre de terminal préférée en définissant les variables d’environnement précédentes.

## <a name="step-1-log-in-to-azure"></a>Étape 1. Connexion à Azure
La première étape consiste à ouvrir l’interface de ligne de commande à partir de l’application AMLWorkbench (Fichier > Ouvrir l’invite de commandes). Ainsi, vous êtes sûr d’utiliser l’environnement Python approprié et de disposer des commandes de l’interface de ligne de commande ML. 

Ensuite, vous pouvez définir le bon contexte dans votre interface de ligne de commande pour accéder à des ressources Azure et les gérer.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Étape 2. Créer un compte et un espace de travail Azure Machine Learning - Expérimentation

Au cours de cette étape, vous créez un compte d’expérimentation et un espace de travail. Consultez [Concepts Azure Machine Learning](overview-general-concepts.md) pour plus d’informations sur les comptes d’expérimentation et les espaces de travail.

> [!NOTE]
> Les comptes d’expérimentation nécessitent un compte de stockage, lequel est utilisé pour stocker les résultats de vos exécutions d’expérimentations. Le nom du compte de stockage doit être globalement unique dans Azure car une URL y est associée. Si vous ne spécifiez pas un compte de stockage existant, le nom de votre compte d’expérimentation sert à créer un compte de stockage. Veillez à utiliser un nom unique, sans quoi vous obtenez une erreur semblable à celle-ci : _« Le compte de stockage portant le nom \<nom_compte_stockage> est déjà pris. »_ Autrement, vous pouvez utiliser l’argument `--storage` pour fournir un compte de stockage existant.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Étape 2.a. (facultative) Partager un espace de travail avec un collaborateur
Ici, vous pouvez découvrir comment partager l’accès à un espace de travail avec un collègue. Les étapes permettant de partager l’accès à un compte d’expérimentation ou à un projet sont identiques. Seule la façon d’obtenir l’ID de ressource Azure a besoin d’être mise à jour.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> Dans la commande ci-dessus, `bob@contoso.com` doit être une identité Azure AD valide dans le répertoire auquel l’abonnement actuel appartient.

## <a name="step-3-create-a-new-project"></a>Étape 3. Création d'un projet
Notre prochaine étape consiste à créer un projet. Il existe plusieurs façons de démarrer un nouveau projet.

### <a name="create-a-new-blank-project"></a>Créer un projet vide

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Créer un projet avec un modèle de projet par défaut
Vous pouvez créer un projet avec un modèle par défaut.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Créer un projet associé à un dépôt Git cloud
Vous pouvez créer un projet associé à un référentiel Git VSTS (Visual Studio Team Service). Chaque fois qu’une expérience est soumise, un instantané de l’ensemble du dossier du projet est validé dans le dépôt Git distant. Pour plus d’informations, consultez [Utilisation d’un dépôt Git avec un projet Azure Machine Learning Workbench](using-git-ml-project.md).

> [!NOTE]
> Azure Machine Learning prend uniquement en charge les dépôts Git vides créés dans VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Si vous obtenez une erreur qui stipule que l’URL du dépôt n’est peut-être pas valide ou que l’utilisateur n’a peut-être pas accès, vous pouvez créer un jeton de sécurité dans VSTS (sous _Sécurité_, _Ajouter des jetons d’accès personnels_) et utiliser l’argument `--vststoken` lors de la création de votre projet. 

### <a name="sample_create"></a>Créer un projet à partir d’un exemple
Dans cet exemple, vous créez un projet à l’aide d’un exemple de projet comme modèle.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Une fois que votre projet est créé, utilisez la commande `cd` pour entrer dans le répertoire du projet.

## <a name="step-4-run-the-training-experiment"></a>Étape 4. Exécuter l’expérimentation de formation 
Les étapes ci-dessous partent du principe que vous avez un projet qui utilise l’exemple Iris (consultez [Créer un projet à partir d’un exemple](#sample_create)).

### <a name="prepare-your-environment"></a>Préparation de votre environnement 
Pour l’exemple Iris, vous devez installer matplotlib.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Soumettre l’expérimentation

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Itérer au sein de votre expérimentation avec des taux de régularisation décroissants
En faisant preuve d’un peu de créativité, vous pouvez facilement former un script Python qui envoie des expérimentations avec des taux de régularisation différents. (Vous devrez peut-être modifier le fichier pour pointer vers le chemin de projet approprié.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Étape 5. Afficher l’historique des exécutions
La commande suivante liste toutes les exécutions précédentes qui ont eu lieu. 

```azure-cli
$ az ml history list -o table
```
L’exécution de la commande précédente affiche la liste de toutes les exécutions qui appartiennent à ce projet. Vous pouvez voir que les mesures des taux de précision et de régularisation sont également listées. Ainsi, vous identifiez facilement la meilleure exécution dans la liste.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Étape 5.a. Afficher la pièce jointe créée par une exécution donnée 
Pour afficher la pièce jointe associée à une exécution donnée, vous pouvez utiliser la commande info de l’historique des exécutions. Recherchez l’ID d’une exécution spécifique dans la liste précédente.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Pour télécharger les artefacts d’une exécution, vous pouvez utiliser la commande ci-dessous :

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Étape 6. Promouvoir les artefacts d’une exécution 
L’une des exécutions présente un meilleur AUC, c’est donc celle à utiliser pour créer un service web d’évaluation à déployer en production. Pour cela, vous devez d’abord promouvoir les artefacts dans une ressource.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Cette opération crée un dossier `assets` dans votre répertoire de projet avec un fichier `model.pkl.link`. Ce fichier de liaison est utilisé pour faire référence à une ressource promue.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Étape 7. Télécharger les fichiers à opérationnaliser
Téléchargez le modèle promu et utilisez-le pour créer notre service web de prédiction. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>Étape 8 : Configurer votre environnement de gestion des modèles 
Créez un environnement pour déployer des services web. Vous pouvez exécuter le service web sur l’ordinateur local à l’aide de Docker. Ou bien, déployez-le sur un cluster ACS pour les opérations à grande échelle. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Étape 9. Créer un compte de gestion des modèles 
Un compte de gestion des modèles est exigé pour déployer et suivre vos modèles en production. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Étape 10. Création d’un service web
Créez un service web qui renvoie une prédiction en utilisant le modèle que vous avez déployé. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>Étape 11. Exécuter le service web
À l’aide de l’ID de service web issu de la sortie de l’étape précédente, appelez le service web et testez-le. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>Étape 12. Suppression de toutes les ressources 
Terminons ce didacticiel en supprimant toutes les ressources créées, sauf si vous souhaitez continuer à les utiliser. 

Pour cela, supprimez le groupe de ressources qui contient les ressources. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à utiliser Azure Machine Learning pour : 
> [!div class="checklist"]
> * Configurer un compte d’expérimentation, puis créer un espace de travail
> * Créer des projets
> * Envoyer des expérimentations à plusieurs cibles de calcul
> * Promouvoir et inscrire un modèle formé
> * Créer un compte de gestion des modèles pour la gestion des modèles
> * Créer un environnement pour déployer des services web
> * Déployer un service web et l’évaluer avec de nouvelles données
