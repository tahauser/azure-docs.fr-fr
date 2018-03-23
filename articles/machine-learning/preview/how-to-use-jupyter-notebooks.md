---
title: Guide pratique pour utiliser des blocs-notes Jupyter dans Azure Machine Learning Workbench | Microsoft Docs
description: Un guide d’utilisation de la fonctionnalité Bloc-notes Jupyter d’Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: c21b7096f689efedacd6e7d55d83912d35dff803
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Utiliser un bloc-notes Jupyter dans Azure Machine Learning Workbench

Azure Machine Learning Workbench prend en charge une expérimentation de science des données interactive par le biais de son intégration dans le bloc-notes Jupyter. Cet article décrit comment tirer parti de cette fonctionnalité pour augmenter la vitesse et la qualité de votre expérimentation de science des données interactive.

## <a name="prerequisites"></a>Prérequis

- [Créer des comptes Azure Machine Learning et installer Azure Machine Learning Workbench](quickstart-installation.md).
- Familiarisez-vous avec les [bloc-notes Jupyter](http://jupyter.org/). Cet article n’est pas destiné à vous apprendre comment utiliser Jupyter.

## <a name="jupyter-notebook-architecture"></a>Architecture du bloc-notes Jupyter
À un niveau élevé, le bloc-notes Jupyter inclut trois composants. Chacun s’exécute dans des environnements de calcul différents :

- **Client** : reçoit une entrée d’utilisateur et affiche une sortie rendue.
- **Serveur** : serveur web qui héberge les fichiers de bloc-notes (fichiers .ipynb).
- **Noyau** : environnement d’exécution des cellules de bloc-notes.

Pour plus d’informations, consultez la [documentation Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html) officielle. Le diagramme ci-dessous illustre comment cette architecture client, serveur et noyau est mappée aux composants dans Azure Machine Learning :

![Architecture du bloc-notes Jupyter](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Noyaux dans Azure Machine Learning Workbench
Vous pouvez accéder à des noyaux différents dans Azure Machine Learning Workbench en définissant des configurations d’exécution et des cibles de calcul dans le dossier `aml_config` de votre projet. L’ajout d’une nouvelle cible de calcul en émettant une commande `az ml computetarget attach` revient à ajouter un nouveau noyau.

>[!NOTE]
>Relisez [Configuration du service d’expérimentation Azure Machine Learning](experimentation-service-configuration.md) pour plus d’informations sur les configurations d’exécution et les cibles de calcul.

### <a name="kernel-naming-convention"></a>convention de nommage de noyau
Azure Machine Learning Workbench génère des noyaux Jupyter personnalisés. Ceux-ci sont nommés *\<nom du projet> \<nom de la configuration d’exécution>*. Par exemple, si vous avez une configuration d’exécution nommée _docker-python_ dans un projet nommé _myIris_, Azure Machine Learning rend disponible un noyau nommé *myIris docker-python.* Vous définissez le noyau en cours d’exécution dans le menu **Noyau** du bloc-notes Jupyter, sous-menu **Modifier le noyau**. Le nom du noyau en cours d’exécution apparaît tout à fait à droite de la barre de menus.
 
Actuellement, Azure Machine Learning Workbench prend en charge les types de noyaux suivants.

### <a name="local-python-kernel"></a>Noyau Python local
Ce noyau Python prend en charge l’exécution sur des ordinateurs locaux. Il est intégré à la prise en charge de l’historique des exécutions d’Azure Machine Learning. Le nom du noyau est généralement *my_project_name local.*

>[!NOTE]
>N’utilisez pas le noyau Python 3. Il s’agit d’un noyau autonome fourni par Jupyter par défaut et qui n’est pas intégré dans les fonctionnalités d’Azure Machine Learning. Par exemple, les fonctions magiques Jupyter `%azureml` retournent des erreurs « introuvable ». 

### <a name="python-kernel-in-docker-local-or-remote"></a>Noyau Python dans Docker (local ou distant)
Ce noyau Python s’exécute dans un conteneur Docker, soit sur votre ordinateur local, soit dans une machine virtuelle Linux distante. Le nom du noyau est généralement *my_project docker.* Le champ `Framework` du fichier `docker.runconfig` associé a la valeur `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Noyau PySpark dans Docker (local ou distant)
Ce noyau PySpark exécute des scripts dans un contexte Spark exécuté à l’intérieur d’un conteneur Docker, sur votre ordinateur local ou sur une machine virtuelle Linux distante. Le nom du noyau est généralement *my_project docker.* Le champ `Framework` du fichier `docker.runconfig` associé a la valeur `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Noyau PySpark sur un cluster Azure HDInsight
Ce noyau s’exécute dans le cluster Azure HDInsight distant que vous avez joint en tant que cible de calcul pour votre projet. Le nom du noyau est généralement *my_project my_hdi.* 

>[!IMPORTANT]
>Dans le fichier `.compute` de la cible de calcul HDI, vous devez remplacer la valeur du champ `yarnDeployMode` par `client` (la valeur par défaut est `cluster`) pour pouvoir utiliser ce noyau. 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Démarrer un serveur Jupyter à partir d’Azure Machine Learning Workbench
Dans Azure Machine Learning Workbench, vous pouvez accéder aux blocs-notes via l’onglet **Blocs-notes**. L’exemple de projet _Classifying Iris_ inclut un exemple de bloc-notes `iris.ipynb`.

![Onglet Blocs-notes](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

Quand vous ouvrez un bloc-notes dans Azure Machine Learning Workbench, il s’affiche sous son propre onglet de document en **Mode Aperçu**. Il s’agit d’une vue en lecture seule qui n’exige pas de serveur et de noyau Jupyter en cours d’exécution.

![Aperçu de bloc-notes](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

En sélectionnant le bouton **Start Notebook Server** (Démarrer le serveur de bloc-notes), vous démarrez le serveur Jupyter et vous basculez le bloc-notes en **mode d’édition**. L’interface utilisateur habituelle du bloc-notes Jupyter est alors incorporée dans Workbench. Vous pouvez maintenant définir un noyau à partir du menu **Noyau** et démarrer votre session de bloc-notes interactive. 

>[!NOTE]
>Avec des noyaux non locaux, le démarrage peut prendre une minute ou deux lors de la première utilisation. Vous pouvez exécuter la commande `az ml experiment prepare` à partir de la fenêtre CLI pour préparer la cible de calcul et permettre ainsi au noyau de démarrer beaucoup plus rapidement.

![Mode d’édition](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Voici une expérience de bloc-notes Jupyter entièrement interactive. Toutes les opérations de bloc-notes et tous les raccourcis clavier standard sont pris en charge à partir de cette fenêtre à l’exception de certaines opérations de fichier qui peuvent être effectuées par le biais de l’onglet **Blocs-notes** et de l’onglet **Fichier** de Workbench.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Démarrer un serveur Jupyter à partir de la ligne de commande
Vous pouvez également démarrer une session de bloc-notes en émettant une commande `az ml notebook start` à partir de la fenêtre de ligne de commande :
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
Votre navigateur par défaut s’ouvre automatiquement avec le serveur Jupyter pointant vers le répertoire d’accueil du projet. Vous pouvez également utiliser l’URL et le jeton affichés dans la fenêtre CLI pour ouvrir d’autres fenêtres du navigateur localement. 

![Tableau de bord du projet](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Vous pouvez maintenant sélectionner un fichier de bloc-notes `.ipynb`, l’ouvrir, définir le noyau (s’il n’a pas été défini), puis démarrer votre session interactive.

![Tableau de bord du projet](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Utiliser des commandes magic pour gérer des expérimentations

Vous pouvez utiliser des [commandes magic](http://ipython.readthedocs.io/en/stable/interactive/magics.html) dans vos cellules de bloc-notes pour suivre votre historique d’exécution et enregistrer des sorties, telles que des modèles ou des jeux de données.

Pour suivre les exécutions de cellules de bloc-notes individuelles, utilisez la commande magic `%azureml history on`. Après l’activation de l’historique, chaque exécution de cellule apparaît en tant qu’entrée dans l’historique d’exécution :

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Pour désactiver le suivi de l’exécution de la cellule, utilisez la commande magic `%azureml history off`.

Vous pouvez utiliser la commande magic `%azureml upload` pour enregistrer des fichiers de modèles et de données à partir de votre exécution. Les objets enregistrés apparaissent en tant que sorties dans la vue de l’historique d’exécution :

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Les sorties doivent être enregistrées dans un dossier nommé *outputs.*

## <a name="next-steps"></a>Étapes suivantes
- Pour savoir comment utiliser un bloc-notes Jupyter, consultez la [documentation officielle de Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Pour mieux comprendre l’environnement d’exécution d’une expérimentation Azure Machine Learning, consultez [Configuration du service d’expérimentation Azure Machine Learning](experimentation-service-configuration.md).

