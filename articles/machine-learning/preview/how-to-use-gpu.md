---
title: Guide pratique pour utiliser des GPU dans Azure Machine Learning | Microsoft Docs
description: "Cet article décrit comment utiliser des processeurs graphiques (GPU) pour effectuer l’apprentissage des réseaux neuronaux profonds dans Azure Machine Learning Workbench."
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 4ef5c9ad58f86a669fc5c0bceb085ef2a266a008
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Guide pratique pour utiliser des GPU dans Azure Machine Learning
Les processeurs graphiques (GPU) sont largement utilisés pour traiter des tâches exigeant beaucoup de ressources de calcul, notamment lors de l’apprentissage de certains modèles de réseau neuronal profond. L’utilisation de GPU vous permet de réduire considérablement la durée d’apprentissage des modèles. Dans ce document, vous allez apprendre à configurer Azure Machine Learning Workbench pour utiliser une instance [DSVM (Data Science Virtual Machine)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) dotée de processeurs graphiques en tant que cible d’exécution. 

## <a name="prerequisites"></a>Composants requis
- Pour parcourir ce guide pratique, vous devez d’abord [installer Azure Machine Learning Workbench](quickstart-installation.md).
- Vous devez avoir accès à des ordinateurs équipés de processeurs graphiques (GPU) NVidia.
    - Vous pouvez exécuter vos scripts directement sur l’ordinateur local (Windows ou MacOS) équipé de GPU.
    - Vous pouvez également exécuter des scripts dans un conteneur Docker sur un ordinateur équipé de GPU.

## <a name="execute-in-local-environment-with-gpus"></a>Exécution dans l’environnement _local_ doté de GPU
Vous pouvez installer Azure Machine Learning Workbench sur un ordinateur équipé de GPU et effectuer les exécutions dans l’environnement _local_. Il peut s’agir des ordinateurs suivants :
- Un ordinateur Windows ou macOS physique.
- Une machine virtuelle Windows comme une instance DSVM Windows provisionnée à l’aide du modèle de machine virtuelle série CN Azure.

Dans ce cas, il n’existe aucune configuration particulière exigée dans Azure Machine Learning Workbench. Vérifiez simplement que vous disposez de tous les pilotes, boîtes à outils et bibliothèques d’apprentissage automatique compatibles avec des GPU nécessaires dans votre installation locale. Effectuez tout simplement les exécutions dans l’environnement _local_ où le runtime Python peut accéder directement au matériel GPU local.

1. Ouvrez Azure Machine Learning Workbench. Accédez à **Fichier** et **Ouvrir l’invite de commandes**. 
2. À partir de la ligne de commande, installez le framework d’apprentissage profond compatible avec des GPU, comme Microsoft Cognitive Toolkit, TensorFlow, etc. Par exemple :

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. Écrivez du code Python qui exploite les bibliothèques d’apprentissage profond.
4. Choisissez un environnement Compute _local_ et exécutez le code Python.

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>Exécution dans un environnement _docker_ sur une machine virtuelle Linux dotée de GPU
Azure Machine Learning Workbench prend également en charge l’exécution dans Docker sur une machine virtuelle Linux Azure. Vous avez la possibilité intéressante d’exécuter des travaux exigeant beaucoup de ressources de calcul sur un composant matériel virtuel puissant, et de payer uniquement l’utilisation en la désactivant quand vous avez terminé. Même si en principe il est possible d’utiliser des GPU sur toutes les machines virtuelles Linux, l’image DSVM basée sur Ubuntu est fournie avec les bibliothèques et pilotes CUDA requis préinstallés, ce qui facilite considérablement la configuration. Effectuez les étapes suivantes :

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Créer une machine virtuelle de science des données Linux Ubuntu dans Azure
1. Ouvrez votre navigateur web pour accéder au [portail Azure](https://portal.azure.com).

2. Sélectionnez **+ Nouveau** dans la partie gauche du portail.

3. Recherchez « Data Science Virtual Machine for Linux (Ubuntu) » dans la Place de Marché.

4. Cliquez sur **Créer** pour créer une image DSVM Ubuntu.

5. Renseignez le formulaire **de base** avec les informations requises.
Quand vous sélectionnez l’emplacement de votre machine virtuelle, notez que les machines virtuelles GPU sont uniquement disponibles dans certaines régions Azure, par exemple, **Sud-Centre des États-Unis**. Consultez [Disponibilité des produits de calcul par région](https://azure.microsoft.com/en-us/regions/services/).
Cliquez sur OK pour enregistrer les informations **de base**.

6. Choisissez la taille de la machine virtuelle. Sélectionnez une des tailles de machines virtuelles dont le préfixe est CN et équipées de processeurs graphiques NVidia.  Cliquez sur **Afficher tout** pour afficher la liste complète, selon vos besoins. Découvrez-en plus sur les [machines virtuelles Azure équipées de GPU](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu).

7. Terminez de renseigner les paramètres restants et passez en revue les informations d’achat. Cliquez sur Acheter pour créer la machine virtuelle. Prenez note de l’adresse IP allouée à la machine virtuelle. 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Créer un projet dans Azure Machine Learning Workbench 
Vous pouvez utiliser l’exemple de _classification MNIST avec TensorFlow_ ou l’exemple de _classification MNIST avec CNTK_.

### <a name="create-a-new-compute-target"></a>Créer une cible de calcul
Lancez la ligne de commande depuis Azure Machine Learning Workbench. Entrez la commande ci-après. Remplacez le texte d’espace réservé de l’exemple ci-dessous par vos propres valeurs pour le nom, l’adresse IP, le nom d’utilisateur et le mot de passe. 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>Configurer Azure Machine Learning Workbench pour accéder au GPU
Revenez au projet et ouvrez l’**affichage des fichiers**, puis appuyez sur le bouton **Actualiser**. À présent, vous voyez deux nouveaux fichiers de configuration `my_dsvm.compute` et `my_dsvm.runconfig`.
 
Ouvrez le fichier `my_dsvm.compute`. Remplacez la valeur de `baseDockerImage` par `microsoft/mmlspark:plus-gpu-0.7.9` et ajoutez une nouvelle ligne `nvidiaDocker: true`. Le fichier doit ainsi comporter ces deux lignes :
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
À présent, ouvrez `my_dsvm.runconfig`, modifiez la valeur de `Framework` en remplaçant `PySpark` par `Python`. Si cette ligne est absente, ajoutez-la, étant donné que la valeur par défaut serait `PySpark`.

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>Référencer le framework d’apprentissage profond 
Ouvrez le fichier `conda_dependencies.yml` et vérifiez que vous utilisez la version GPU des packages Python du framework d’apprentissage profond. Les exemples de projets répertorient les versions de processeur, vous devez donc apporter cette modification.

Exemple pour TensorFlow : 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Exemple pour Microsoft Cognitive Toolkit :
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

Vous pouvez également utiliser la version SGD à 1 octet de Microsoft Cognitive Toolkit qui offre des améliorations de performances sur les machines virtuelles à plusieurs GPU. Notez [l’exigence d’une licence pour SGD à 1 octet](https://docs.microsoft.com/en-us/cognitive-toolkit/cntk-1bit-sgd-license).

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Exécuter
Vous êtes maintenant prêt à exécuter vos scripts Python. Vous pouvez les exécuter dans Azure Machine Learning Workbench en utilisant le contexte `my_dsvm` ou vous pouvez les lancer à partir de la ligne de commande :
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
Pour vérifier que le GPU est utilisé, examinez la sortie de l’exécution pour y voir ce qui suit :

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

Félicitations ! Votre script vient d’exploiter la puissance des GPU par le biais d’un conteneur Docker.

## <a name="next-steps"></a>Étapes suivantes
Consultez un exemple d’utilisation d’un GPU pour accélérer l’apprentissage de réseaux neuronaux profonds dans la galerie Azure Machine Learning.
