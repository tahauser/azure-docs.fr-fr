---
title: Installer et utiliser l’interface CLI Machine Learning pour les principales tâches Azure Machine Learning
description: Découvrez comment installer et utiliser l’interface CLI pour les principales tâches dans Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: mwinkler
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/10/2018
ms.openlocfilehash: f34c247728c854c47f486925d440eee0dc5b1945
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Installer et utiliser l’interface CLI Machine Learning pour les principales tâches Azure Machine Learning

Les services Azure Machine Learning forment une solution d’analytique avancée et de science des données de bout en bout intégrée. Les scientifiques des données professionnels peuvent utiliser les services Azure Machine Learning pour préparer des données, développer des expériences et déployer des modèles à l’échelle du cloud. 

Azure Machine Learning fournit une interface de ligne de commande (CLI) avec laquelle vous pouvez :
+ Gérer votre espace de travail et vos projets
+ Configurer des cibles de calcul
+ Exécuter des expériences de formation
+ Afficher l’historique et les métriques des exécutions passées
+ Déployer des modèles en production en tant que services web
+ Gérer des modèles et services de production

Cet article présente quelques-unes des commandes CLI les plus utiles. 

![Interface CLI Azure Machine Learning](media/cli-for-azure-machine-learning/flow.png)

>[!NOTE]
>L’interface CLI fournie avec les services Azure Machine Learning est différente de l’[interface de ligne de commande Azure](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest), utilisée pour la gestion des ressources Azure.

## <a name="get-and-start-cli"></a>Obtenir et démarrer l’interface CLI

Pour obtenir cette interface CLI, installez Azure Machine Learning Workbench, téléchargeable à cette adresse :
    + Windows - https://aka.ms/azureml-wb-msi 
    + MacOS - https://aka.ms/azureml-wb-dmg 

Pour démarrer l’interface CLI :
+ Dans Azure Machine Learning Workbench, lancez l’interface CLI à partir du menu **Fichier -> Ouvrir l’invite de commandes.**

## <a name="get-command-help"></a>Obtenir de l’aide sur les commandes 

Vous pouvez obtenir des informations supplémentaires sur les commandes CLI en utilisant `--debug` ou `--help` après les commandes comme `az ml <xyz> --debug` où `<xyz>` est le nom de la commande. Par exemple : 
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Tâches d’interface CLI courantes pour Azure Machine Learning 

Découvrez-en plus sur les tâches les plus courantes que vous pouvez effectuer avec l’interface CLI dans cette section, notamment :
+ [Configuration des cibles de calcul](#target)
+ [Envoi de travaux pour une exécution à distance](#jobs)
+ [Utilisation de bloc-notes Jupyter](#jupyter)
+ [Interaction avec les historiques des exécutions](#history)
+ [Configuration de votre environnement de fonctionnement](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Configurer une cible de calcul

Vous pouvez calculer votre modèle Machine Learning dans différentes cibles, notamment :
+ En mode local
+ Dans une machine virtuelle DSVM
+ Dans un cluster HDInsight

Pour attacher une cible de machine virtuelle DSVM :
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Pour attacher une cible HDInsight :
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

Dans le dossier **aml_config**, vous pouvez modifier les dépendances conda. 

En outre, vous pouvez utiliser PySpark, Python ou Python dans une machine virtuelle DSVM GPU. 

Pour définir le mode de fonctionnement Python :
+ Pour Python, ajoutez `Framework:Python` dans `<target name>.runconfig` 

+ Pour PySpark, ajoutez `Framework:PySpark` dans `<target name>.runconfig` 

+ Pour Python dans une machine virtuelle DSVM GPU,
    1. Ajoutez `Framework:Python` dans `<target name>.runconfig` 

    1. De plus, ajoutez `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` dans `<target name>.compute`

Pour préparer la cible de calcul :
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Pour afficher votre abonnement :<br/>
>`az account show`<br/>
>
>Pour définir votre abonnement :<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Envoyer des travaux à distance

Pour envoyer un travail à une cible distante :
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Utiliser des bloc-notes Jupyter

Pour démarrer un bloc-notes Jupyter :
```azurecli
az ml notebook start
```

Cette commande démarre un bloc-notes Jupyter dans localhost. Vous pouvez travailler localement en sélectionnant le noyau Python 3 ou sur votre machine virtuelle distante en sélectionnant le noyau `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Interagir avec l’historique des exécutions et l’explorer

Pour lister l’historique des exécutions :
```azurecli
az ml history list -o table
```

Pour lister toutes les exécutions terminées :
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Pour trouver les exécutions les plus précises :
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Vous pouvez aussi télécharger les fichiers générés par chaque exécution. 

Pour promouvoir un modèle enregistré dans le dossier outputs :
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Pour télécharger ce modèle :
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Configurer votre environnement de fonctionnement

Pour configurer votre environnement de fonctionnement, vous devez créer :

> [!div class="checklist"]
> * Un groupe de ressources. 
> * Un compte de stockage
> * Un ACR (Azure Container Registry)
> * Un compte Application Insights
> * Un déploiement Kubernetes sur un cluster ACS


Pour configurer un déploiement local à des fins de test dans un conteneur Docker :
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Pour configurer un cluster ACS avec Kubernetes :
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Pour surveiller l’état du déploiement :
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Pour définir l’environnement à utiliser :
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

Commencez par l’un de ces articles : 
+ [Installer Azure Machine Learning et commencer à l’utiliser](quickstart-installation.md)
+ [Didacticiel Classification des données d’iris : partie 1](tutorial-classifying-iris-part-1.md)

Approfondissez avec l’un de ces articles :
+ [Commandes CLI pour la gestion des modèles](model-management-cli-reference.md)