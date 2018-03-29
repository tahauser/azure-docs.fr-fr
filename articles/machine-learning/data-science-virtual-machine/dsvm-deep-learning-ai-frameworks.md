---
title: Frameworks d’apprentissage profond et d’intelligence artificielle - Azure | Microsoft Docs
description: Frameworks d’apprentissage profond et d’intelligence artificielle
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 829095c7f9b92f64fd6204481e68b2594a3a0017
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>Frameworks d’apprentissage profond et d’intelligence artificielle
La machine virtuelle [DSVM](http://aka.ms/dsvm) (Data Science Virtual Machine) et la machine virtuelle [DLVM](http://aka.ms/dsvm/deeplearning) (Deep Learning Virtual Machine) prennent en charge plusieurs frameworks d’apprentissage profond pour créer des applications d’intelligence artificielle (IA) avec des fonctionnalités cognitives et d’analytique prédictive comme la compréhension d’image et de langue. 

Voici les détails de tous les frameworks d’apprentissage profond disponibles sur la machine virtuelle DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Microsoft Cognitive Toolkit (CNTK) est installé sur Python 2.7, dans l’environnement _root_, ainsi que sur Python 3.5, dans l’environnement _py35_.   |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | Keras      |
| Comment l’utiliser/l’exécuter ?    | Ouvrez Jupyter, puis recherchez le dossier CNTK.  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Sur Linux, TensorFlow est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_). Sur Windows, Tensorflow est installé dans l’environnement Python 3.5 (_py35_).  |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | Keras      |
| Comment l’utiliser/l’exécuter ?    | Ouvrez Jupyter, puis recherchez le dossier TensorFlow.  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Keras est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_).   |
| Liens vers des exemples      | https://github.com/fchollet/keras/tree/master/examples      |
| Outils connexes sur la machine virtuelle DSVM      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| Comment l’utiliser/l’exécuter ?    | Téléchargez les exemples à partir de l’emplacement Github, copiez-le dans un répertoire sous ~/notebooks et ouvrez-le dans Jupyter.   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Caffe est installé dans `/opt/caffe`.    |
| Liens vers des exemples      | Des exemples sont inclus dans `/opt/caffe/examples`.      |
| Outils connexes sur la machine virtuelle DSVM      | Caffe2      |
### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?  

Utilisez X2Go pour vous connecter à votre machine virtuelle, puis démarrez un nouveau terminal et entrez

```
cd /opt/caffe/examples
jupyter notebook
```

Une nouvelle fenêtre de navigateur s’ouvre avec les exemples de bloc-notes.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Caffe2 est installé dans `/opt/caffe2`. Il est également disponible pour l’environnement conda Python 2.7 (_root_).     |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | Caffe      |
| Comment l’utiliser/l’exécuter ?    | Ouvrez Jupyter, puis accédez au répertoire Caffe2 pour rechercher des exemples de bloc-notes. Certains bloc-notes exigent que la racine Caffe2 soit définie dans le code Python. Entrez /opt/caffe2.   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Chainer est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_). ChainerRL et ChainerCV sont également installés.   |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus.      |
| Outils connexes sur la machine virtuelle DSVM      | Caffe      |

### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?  

Sur un terminal, activez la version de Python souhaitée (_root_ ou _py35_), exécutez _python_, puis importez Chainer. Dans Jupyter, sélectionnez le noyau Python 2.7 ou 3.5, puis importez Chainer.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond pour H2O      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Deep Water est installé dans `/dsvm/tools/deep_water`.   |
| Liens vers des exemples      | Des exemples sont disponibles par le biais du serveur Deep Water.      |
| Outils connexes sur la machine virtuelle DSVM      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?  

Connectez-vous à la machine virtuelle à l’aide de X2Go. Sur un terminal, démarrez le serveur Deep Water :

    java -jar /dsvm/tools/deep_water/h2o.jar

Ensuite, ouvrez un navigateur et connectez-vous à `http://localhost:54321`.



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | MXNet est installé dans `C:\dsvm\tools\mxnet` sur Windows et `/dsvm/tools/mxnet` sur Linux. Les liaisons Python sont installées dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_). Les liaisons R sont également installées.   |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus.    |
| Outils connexes sur la machine virtuelle DSVM      | Keras      |
| Comment l’utiliser/l’exécuter ?    | Ouvrez Jupyter, puis recherchez le dossier mxnet.  |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Système d’apprentissage profond de NVIDIA permettant une formation rapide des modèles d’apprentissage profond.      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | DIGITS est installé dans `/dsvm/tools/DIGITS` et disponible en tant que service appelé _digits_.   |
### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?  

Connectez-vous à la machine virtuelle avec X2Go. Sur un terminal, démarrez le service :

    sudo systemctl start digits

Le démarrage du service prend environ une minute. Démarrez un navigateur web et accédez à `http://localhost:5000`.



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil NVIDIA pour interroger l’activité du GPU      |
| Éditions DSVM prises en charge      | Windows, Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | _nvidia-smi_ est disponible sur le chemin système.   |
| Comment l’utiliser/l’exécuter ? | Démarrez une invite de commandes (sur Windows) ou un terminal (sur Linux), puis exécutez _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Theano est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_).   |
| Outils connexes sur la machine virtuelle DSVM      | Keras      |
| Comment l’utiliser/l’exécuter ?    | Sur un terminal, activez la version de Python souhaitée (root ou py35), exécutez python, puis importez Theano. Dans Jupyter, sélectionnez le noyau Python 2.7 ou 3.5, puis importez Theano.  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Ubuntu     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Torch est installé dans `/dsvm/tools/torch`. PyTorch est installé dans les environnements Python 2.7 (_root_) et Python 3.5 (_py35_).   |
| Liens vers des exemples      | Des exemples Torch se trouvent dans `/dsvm/samples/torch`. Des exemples PyTorch se trouvent dans `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Framework d’apprentissage profond      |
| Éditions DSVM prises en charge      | Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | PyTorch est installé dans l’environnement Python 3.5 (_py35_).   |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter sont inclus, et vous trouverez aussi des exemples dans /dsvm/samples/pytorch.      |
| Outils connexes sur la machine virtuelle DSVM      | Torch      |

### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?  

Sur un terminal, exécutez _python_, puis importez torch. Dans Jupyter, sélectionnez le noyau Python 3.5, puis importez torch.


## <a name="mxnet-model-server"></a>MXNet Model Server

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Un serveur pour créer des points de terminaison HTTP pour les modèles MXNet et ONNX.      |
| Éditions DSVM prises en charge      | Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | _mxnet-model-server_ est disponible sur le terminal.   |
| Liens vers des exemples      | Recherchez des exemples mis à jour dans la page [MXNet Model Server](https://github.com/awslabs/mxnet-model-server).    |
| Outils connexes sur la machine virtuelle DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow Serving

|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Un serveur pour exécuter l’inférence sur un modèle TensorFlow.      |
| Éditions DSVM prises en charge      | Linux     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | _tensorflow_model_server_ est disponible sur le terminal.   |
| Liens vers des exemples      | D’autres exemples sont disponibles [en ligne](https://www.tensorflow.org/serving/).      |
| Outils connexes sur la machine virtuelle DSVM      | TensorFlow      |
