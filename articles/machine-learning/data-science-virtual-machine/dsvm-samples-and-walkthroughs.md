---
title: "Exemples et procédures pas à pas sur la machine virtuelle DSVM - Azure | Microsoft Docs"
description: "Exemples et procédures pas à pas sur la machine virtuelle DSVM"
keywords: "outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: fb9a7e900c489b3d22594dfcb6e1faa736c3c490
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>Exemples sur les machines virtuelles DSVM (Data Science Virtual Machine)

Les machines virtuelles DSVM comportent des exemples complets, la plupart sous la forme de bloc-notes Jupyter. Vous pouvez accéder à Jupyter en cliquant sur l’icône `Jupyter` sur le Bureau ou dans le menu de l’application.  
> [!NOTE]
> Reportez-vous à la section [Blocs-notes Jupyter](#access-jupyter) pour activer les blocs-notes Jupyter sur votre machine virtuelle DSVM.

## <a name="quick-reference-of-samples"></a>Référence rapide d’exemples
| Catégorie d’exemples | Description | Emplacements |
| ------------- | ------------- | ------------- |
| Langage **R**  | Exemples en **R** expliquant des scénarios tels que la connexion à des magasins de données cloud Azure, la comparaison d’Open Source R et de Microsoft R et la mise en application de modèles sur Microsoft R Server ou SQL Server. <br/> [Capture d’écran](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Langage **Python**  | Exemples en **Python** expliquant des scénarios tels que la connexion à des magasins de données cloud Azure et l’utilisation **d’Azure Machine Learning**.  <br/> [Capture d’écran](#python-language) | <br/>`~notebooks` <br/><br/>|
| Langage **Julia**  | Exemples en **Julia** qui décrivent la réalisation de graphiques en Julia, l’apprentissage profond en Julia, l’appel de C et Python depuis Julia, etc. <br/> [Capture d’écran](#julia-language) |<br/> **Windows** :<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> **Linux** :<br/> `~notebooks/julia`<br/><br/> |
| **CNTK (COMPUTATIONAL NETWORK TOOLKIT DE MICROSOFT RESEARCH)** <br/> (Microsoft Cognitive Toolkit)  | Exemples d’apprentissage profond publiés par l’équipe Microsoft Cognitive Toolkit.  <br/> [Capture d’écran](#cntk) | <br/>**Windows** :<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> **Linux** :<br/> `~notebooks/CNTK`<br/> <br/>|
| Blocs-notes **MXnet**  | Exemples d’apprentissage profond utilisant des réseaux neuronaux basés sur **MXNet**. Il existe une multitude de blocs-notes allant des scénarios d’initiation aux scénarios avancés.  <br/> [Capture d’écran](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| **Azure Machine Learning** AzureML  | Interaction avec **Azure Machine Learning** Studio et création de points de terminaison de service web à partir de modèles formés localement, pour les workflows de notation informatiques. <br/> [Capture d’écran](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | Exemples d’apprentissage profond utilisant des réseaux neuronaux basés sur **caffe2**. Il existe plusieurs blocs-notes conçus pour familiariser les utilisateurs avec caffe2 et sur la façon de l’utiliser efficacement, y compris des exemples comme le prétraitement des images, la création de jeux de données, la régression et l’utilisation de modèles préformés. <br/> [Capture d’écran](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | Exemples basés sur Python utilisant **H2O** pour de nombreux problèmes autour de scénarios concrets. <br/> [Capture d’écran](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Langage **SparkML**  | Exemple utilisant les fonctionnalités et capacités du Kit de ressources **MLlib** de Spark par le biais de **pySpark 2.0** sur **Apache Spark 2.0**.  <br/> [Capture d’écran](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| Langage **MMLSpark**  | Variété d’exemples utilisant **MMLSpark - Microsoft Machine Learning pour Apache Spark**, framework qui fournit une série d’outils d’apprentissage profond et de science des données pour **Apache Spark**. <br/> [Capture d’écran](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| **TensorFlow**  | Plusieurs exemples et techniques de réseau neuronal implémentés à l’aide du framework **TensorFlow**. <br/> [Capture d’écran](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| **XGBoost** | Exemples d’apprentissage automatique standard dans **XGBoost** pour les scénarios tels que la classification ou la régression. <br/> [Capture d’écran](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Accéder à Jupyter 

Visitez la page d’accueil de Jupyter en accédant à **`https://localhost:9999`** sur Windows ou à **`https://localhost:8000`** sur Ubuntu.


### <a name="enabling-jupyter-access-from-browser"></a>Activation de l’accès à Jupyter à partir du navigateur

**Machine virtuelle DSVM Windows**

Exécutez **`Jupyter SetPassword`** à partir du raccourci du Bureau et suivez l’invite afin de définir/réinitialiser votre mot de passe pour Jupyter et démarrer le processus Jupyter. 
<br/>![Activer l’exception Jupyter](./media/jupyter-setpassword.png)<br/>
Après avoir correctement démarré le processus Jupyter sur votre machine virtuelle, vous pouvez accéder à la page d’accueil de Jupyter en vous rendant sur **`https://localhost:9999`** depuis votre navigateur. Consultez la capture d’écran pour voir comment ajouter l’exception et activer l’accès à Jupyter par le biais du navigateur.
<br/>![Activer l’exception Jupyter](./media/windows-jupyter-exception.png)<br/>
Connectez-vous avec le nouveau mot de passe que vous venez de définir.
<br/>
**Machine virtuelle DSVM Linux**

Vous pouvez accéder à la page d’accueil de Jupyter sur votre machine virtuelle en vous rendant sur **`https://localhost:8000`** depuis votre navigateur. Consultez la capture d’écran pour voir comment ajouter l’exception et activer l’accès à Jupyter par le biais du navigateur.
<br/>![Activer l’exception Jupyter](./media/ubuntu-jupyter-exception.png)<br/>
Connectez-vous avec le même mot de passe que pour la connexion à la machine virtuelle DSVM.
<br/>

**Page d’accueil de Jupyter**
<br/>![Page d’accueil de Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Langage R 
<br/>![Exemples R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Langage Python
<br/>![Exemples Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Langage Julia 
<br/>![Exemples Julia](./media/julia-samples.png)<br/>

## <a name="cntk"></a>CNTK (Computational Network Toolkit de Microsoft Research) 
<br/>![Exemples CNTK](./media/cntk-samples2.png)<br/>
<br/>![Exemples CNTK](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![Exemples MXnet](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Exemples AzurekML](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>caffe2 
<br/>![Exemples caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Exemples H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Exemples SparkML](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Exemples TensorFlow](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![Exemples XGBoost](./media/xgboost-samples.png)<br/>

