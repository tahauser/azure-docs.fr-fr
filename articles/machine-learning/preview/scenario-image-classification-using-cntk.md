---
title: "Classification d’images à l’aide de CNTK dans Azure Machine Learning Workbench | Microsoft Docs"
description: "Formez, évaluez et déployez un modèle de classification d’images personnalisé à l’aide d’Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 03fdd1265464355a2787eff897eb4f70faa095b0
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Classification d’images à l’aide d’Azure Machine Learning Workbench

Les approches relatives à la classification d’images permettent de résoudre un grand nombre de problèmes liés à la vision par ordinateur.
Elles incluent la création de modèles qui répondent à des questions telles que : *est-ce qu’un OBJET est présent dans l’image ?* où OBJET est par exemple un *chien*, une *voiture* ou un *bateau*. Il peut également s’agir de questions plus complexes, par exemple : *Quel est le degré de gravité de la maladie oculaire mise en évidence par le scanner de la rétine de ce patient ?*.

Ce didacticiel décrit la résolution des problèmes de ce genre. Nous montrons comment former, évaluer et déployer votre propre modèle de classification d’images à l’aide de [Microsoft Cognitive Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) dans le cadre de l’apprentissage profond (« deep learning »).
Des exemples d’images sont fournis, mais le lecteur peut également apporter son propre jeu de données et former ses propres modèles personnalisés.

De manière générale, les solutions de vision par ordinateur nécessitaient une expertise appropriée pour identifier et mettre en œuvre manuellement ce que l’on appelle les *caractéristiques*, c’est-à-dire les éléments qui mettent en évidence les informations souhaitées dans des images.
Cette approche manuelle a changé en 2012 avec le fameux article sur [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] et l’apprentissage profond. À l’heure actuelle, les réseaux de neurones profonds (DNN) sont utilisés pour trouver automatiquement ces caractéristiques.
Les réseaux de neurones profonds ont conduit à une énorme amélioration du domaine, non seulement pour la classification d’images, mais aussi pour d’autres problèmes de vision par ordinateur, par exemple la détection d’objets et la similarité d’images.


## <a name="link-to-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Vue d'ensemble

Ce didacticiel est divisé en trois parties :

- La partie 1 montre comment former, évaluer et déployer un système de classification d’images en utilisant un réseau de neurones profond soumis à un préapprentissage en tant que générateur de caractéristiques et en soumettant à un apprentissage une machine à vecteurs de support en sortie.
- La partie 2 montre ensuite comment améliorer la précision en affinant, par exemple, le réseau de neurones profond au lieu de l’utiliser comme un générateur de caractéristiques fixe.
- La partie 3 explique comment utiliser votre propre jeu de données à la place des exemples d’images fournis et, le cas échéant, comment produire votre propre jeu de données à partir d’images provenant d’Internet.

Bien qu’une expérience antérieure de l’apprentissage automatique (« machine learning ») et de CNTK ne soit pas indispensable, cela peut vous aider à comprendre les principes sous-jacents. Les valeurs de précision, les temps d’apprentissage, etc., indiqués dans le didacticiel ne sont que des références. Les valeurs réelles liées à l’exécution du code diffèrent très probablement.


## <a name="prerequisites"></a>configuration requise

Cet exemple nécessite les prérequis suivants :

1. Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).
2. [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installé selon les instructions du [guide d’installation et de démarrage rapide](./quickstart-installation.md) pour installer le programme et créer un espace de travail.  
3. Une machine Windows. Le système d’exploitation Windows est nécessaire, car Workbench prend en charge uniquement Windows et macOS, alors que Microsoft Cognitive Toolkit (que nous utilisons en tant que bibliothèque d’apprentissage profond) prend en charge uniquement Windows et Linux.
4. Vous n’avez pas besoin d’un GPU dédié pour former la machine à vecteurs de support dans la partie 1, mais vous en avez besoin pour affiner le réseau de neurones profond décrit dans la partie 2. Si vous n’avez pas un GPU puissant, si vous souhaitez former avec plusieurs GPU ou si vous n’avez pas de machine Windows, utilisez la machine virtuelle DLVM (Deep Learning Virtual Machine), qui est dotée du système d’exploitation Windows. Vous trouverez [ici](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) un guide de déploiement en 1 clic. Une fois le déploiement effectué, connectez-vous à la machine virtuelle via une connexion Bureau à distance, installez Workbench, puis exécutez le code localement à partir de la machine virtuelle.
5. Diverses bibliothèques Python telles que OpenCV doivent être installées. Cliquez sur *Ouvrir l’invite de commandes* à partir du menu *Fichier* dans Workbench et exécutez les commandes suivantes pour installer ces dépendances :  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` après avoir téléchargé le fichier OpenCV au format wheel depuis http://www.lfd.uci.edu/~gohlke/pythonlibs/ (le nom exact du fichier et sa version peuvent changer)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>Résolution des problèmes / Bogues connus
- Un GPU est nécessaire pour la partie 2. Sinon, une erreur indiquant que « l’apprentissage avec normalisation par lot sur l’UC n’est pas encore implémenté » est générée quand vous essayez d’affiner le réseau de neurones profond.
- Vous pouvez éviter les erreurs relatives à une insuffisance de mémoire durant l’apprentissage du réseau de neurones profond en réduisant la taille du minilot (variable `cntk_mb_size` dans `PARAMETERS.py`).
- Le code a été testé à l’aide de CNTK 2.2. Il doit également fonctionner sur les versions plus récentes (jusqu’à la version 2.0) sans changements ou seulement avec des changements mineurs.
- Au moment de la rédaction de ce document, Azure Machine Learning Workbench avait des problèmes avec les Notebooks de plus de 5 Mo. Un Notebook peut atteindre cette taille s’il est enregistré avec toutes les sorties de cellules affichées. Si vous rencontrez cette erreur, ouvrez l’invite de commandes à partir du menu Fichier dans Workbench, exécutez `jupyter notebook`, ouvrez le Notebook, effacez toutes les sorties, puis enregistrez le Notebook. Une fois que vous avez effectué ces étapes, le Notebook s’ouvre à nouveau correctement dans Azure Machine Learning Workbench.
- Tous les scripts fournis dans cet exemple doivent être exécutés localement et non, par exemple, dans un environnement Docker distant. Tous les Notebooks doivent être exécutés avec le noyau défini sur le noyau de projet local portant le nom « nom_projet local » (par exemple, « myImgClassUsingCNTK local »).

    
## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Pour créer un projet à l’aide de cet exemple en tant que modèle :
1.  Ouvrez Azure Machine Learning Workbench.
2.  Dans la page **Projets**, cliquez sur le signe **+** et sélectionnez **Nouveau projet**.
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet.
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Classification d’images », puis sélectionnez le modèle.
5.  Cliquez sur **Créer**.

L’exécution de ces étapes permet de créer la structure de projet illustrée ci-dessous. Le répertoire du projet est limité à moins de 25 Mo, car Azure Machine Learning Workbench crée une copie de ce dossier après chaque exécution (pour activer l’historique des exécutions). Ainsi, tous les fichiers image et fichiers temporaires sont enregistrés dans et depuis le répertoire *~/Desktop/imgClassificationUsingCntk_data* (appelé *DATA_DIR* dans ce document).

  Dossier| Description
  ---|---
  aml_config/|                           Répertoire contenant les fichiers config d’Azure Machine Learning Workbench
  libraries/|                              Répertoire contenant toutes les fonctions d’assistance Python et Jupyter
  notebooks/|                              Répertoire contenant tous les Notebook
  resources/|                              Répertoire contenant toutes les ressources (par exemple l’url d’images de mode)
  scripts/|                              Répertoire contenant tous les scripts
  PARAMETERS.py|                       Script Python spécifiant tous les paramètres
  readme.md|                           Ce fichier readme


## <a name="data-description"></a>Description des données

Ce didacticiel utilise comme exemple un jeu de données de motifs de vêtements pour le haut du corps comprenant jusqu’à 428 images. Chaque image est annotée selon sa correspondance avec l’un des trois motifs distincts (pois, rayures, léopard). Nous avons réduit le nombre d’images pour que ce didacticiel puisse être exécuté rapidement. Toutefois, le code a été testé rigoureusement et fonctionne avec des dizaines de milliers d’images ou plus. Toutes les images ont été récupérées à l’aide de l’outil Recherche d’images Bing et annotées à la main, comme expliqué dans la [Partie 3](#using-a-custom-dataset). Les URL des images et leurs attributs respectifs sont listés dans le fichier */resources/fashionTextureUrls.tsv*.

Le script `0_downloadData.py` télécharge toutes les images dans le répertoire *DATA_DIR/images/fashionTexture/*. Sur les 428 URL, certaines sont probablement rompues. Ce n’est pas un problème. Cela signifie simplement que nous avons un peu moins d’images pour l’apprentissage et les tests. Tous les scripts fournis dans cet exemple doivent être exécutés localement et non, par exemple, dans un environnement Docker distant.

L’illustration suivante montre des exemples pour les attributs correspondant aux pois (à gauche), aux rayures (au milieu) et au style léopard (à droite). Les annotations ont été faites en fonction du vêtement pour le haut du corps.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>Partie 1 - Apprentissage et évaluation du modèle

Dans la première partie de ce didacticiel, nous formons un système qui utilise, mais ne modifie pas, un réseau de neurones profond préformé. Ce réseau de neurones profond soumis à un préapprentissage sert de générateur de caractéristiques. De plus, une machine à vecteurs de support linéaire est formée pour prédire l’attribut (pois, rayures ou léopard) d’une image donnée.

Nous décrivons maintenant cette approche en détail, pas à pas, et montrons quels scripts doivent être exécutés. Nous recommandons après chaque étape d’inspecter les fichiers écrits, et d’identifier l’emplacement où ils sont écrits.

Tous les paramètres importants sont spécifiés et une brève explication est fournie au même endroit, dans le fichier `PARAMETERS.py`.




### <a name="step-1-data-preparation"></a>Étape 1 : Préparation des données
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

Le Notebook `showImages.ipynb` permet de visualiser les images et de corriger leur annotation selon les besoins. Pour exécuter le Notebook, ouvrez-le dans Azure Machine Learning Workbench, cliquez sur Démarrer le serveur Notebook, si cette option est affichée, basculez sur le noyau de projet local portant le nom « nom_projet local » (par exemple, « myImgClassUsingCNTK local »), puis exécutez toutes les cellules du Notebook. Consultez la section de résolution des problèmes de ce document si vous obtenez une erreur indiquant que le Notebook est trop volumineux pour être affiché.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Exécutez maintenant le script nommé `1_prepareData.py`, qui affecte toutes les images au jeu d’apprentissage ou au jeu de test. Cette affectation est mutuellement exclusive : aucune image d’apprentissage n’est utilisée pour les tests, et vice versa. Par défaut, 75 % des images de chaque classe d’attributs sont affectés aléatoirement à l’apprentissage, et les 25 % restants sont affectés aux tests. Toutes les données générées par le script sont enregistrées dans le dossier *DATA_DIR/proc/fashionTexture/*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>Étape 2 : Affinement du réseau de neurones profond
`Script: 2_refineDNN.py`

Comme nous l’avons expliqué dans la partie 1 de ce didacticiel, le réseau de neurones profond préformé reste fixe (c’est-à-dire qu’il n’est pas affiné). Toutefois, le script nommé `2_refineDNN.py` est toujours exécuté dans la partie 1, car il charge un modèle [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] préformé et le modifie, par exemple, pour permettre une résolution d’image en entrée plus élevée. Cette étape est rapide (secondes) et ne nécessite pas de GPU.

Dans la partie 2 du didacticiel, en raison d’une modification apportée au fichier PARAMETERS.py, le script `2_refineDNN.py` affine également le réseau de neurones profond préformé. Par défaut, nous effectuons 45 époques d’apprentissage durant l’affinement.

Dans les deux cas, le modèle final est ensuite écrit dans le fichier *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>Étape 3 : Évaluer le réseau de neurones profond pour toutes les images
`Script: 3_runDNN.py`

Nous pouvons maintenant utiliser le réseau de neurones profond (éventuellement affiné) issu de la dernière étape pour générer les caractéristiques de nos images. Si une image est utilisée en entrée pour le réseau de neurones profond, la sortie correspond au vecteur de 512 floats de l’avant-dernière couche du modèle. Ce vecteur a une dimension beaucoup plus petite que celle de l’image proprement dite. Toutefois, il doit contenir (et même mettre en évidence) toutes les informations pertinentes de l’image qui permettent de reconnaître l’attribut de l’image, en d’autres termes, d’identifier si l’élément vestimentaire a un motif à pois, à rayures ou léopard.

Toutes les représentations d’images du réseau de neurones profond sont enregistrées dans le fichier *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>Étape 4 : Apprentissage de la machine à vecteurs de support
`Script: 4_trainSVM.py`

Les représentations de 512 floats calculées à la dernière étape sont maintenant utilisées pour former un classifieur de machine à vecteurs de support : à partir d’une image en entrée, la machine à vecteurs de support génère un score pour chaque attribut devant être présent. Dans notre exemple de jeu de données, cela signifie un score pour les « rayures », les « pois » et le style « léopard ».

Le script `4_trainSVM.py` charge les images d’apprentissage, apprend à une machine à vecteurs de support les différentes valeurs du paramètre C de régulation (marge), puis maintient la précision la plus élevée possible pour la machine à vecteurs de support. La précision de la classification est affichée sur la console et tracée dans Workbench. Pour les données fournies relatives au motif, ces valeurs doivent être comprises à peu près entre 100 % et 88 %, respectivement. Enfin, la machine à vecteurs de support formée est écrite dans le fichier *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>Étape 5 : Évaluation et visualisation
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

Vous pouvez mesurer la précision du classifieur d’images formé à l’aide du script `5_evaluate.py`. Le script attribue un score à toutes les images de test à l’aide du classifieur de la machine à vecteurs de support formé, affecte à chaque image l’attribut ayant le score le plus élevé, puis compare les attributs prédits avec les annotations réelles.

La sortie du script `5_evaluate.py` est affichée ci-dessous. La précision de classification de chaque classe est calculée, ainsi que la précision du jeu de test complet (« précision globale ») et la moyenne des précisions individuelles (« moyenne des précisions de l’ensemble des classes »). 100 % correspond à la meilleure précision possible et 0 % à la pire. Une estimation aléatoire produit en moyenne une précision moyenne par classe de 1 par rapport au nombre d’attributs : dans notre cas, cette précision est de 33,33 %. Ces résultats s’améliorent de manière significative avec une résolution d’entrée plus élevée, par exemple `rf_inputResoluton = 1000`. Toutefois, cela implique en contrepartie des temps de calcul plus longs pour le réseau de neurones profond.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

En plus de la précision, la courbe ROC est tracée avec l’aire sous la courbe respective (à gauche). La matrice de confusion est également affichée (à droite) :

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Enfin, le Notebook `showResults.py` est fourni pour faire défiler les images de test et visualiser leurs scores de classification respectifs. Comme expliqué à l’étape 1, chaque Notebook dans cet exemple doit utiliser le noyau de projet local portant le nom « nom_projet local » :
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>Étape 6 : Déploiement
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

Le système formé peut maintenant être publié en tant qu’API REST. Le déploiement est expliqué dans le Notebook `deploy.ipynb` et repose sur les fonctionnalités d’Azure Machine Learning Workbench (pensez à définir le noyau en tant que noyau de projet local portant le nom « nom_projet local »). Pour plus d’informations sur le déploiement, consultez également l’excellente section consacrée au déploiement dans le [didacticiel IRIS](tutorial-classifying-iris-part-3.md).

Une fois déployé, le service web peut être appelé à l’aide du script `6_callWebservice.py`. Notez que l’adresse IP (locale ou dans le cloud) du service web doit être définie en premier dans le script. Le Notebook `deploy.ipynb` explique comment trouver cette adresse IP.








## <a name="part-2---accuracy-improvements"></a>Partie 2 - Améliorations de la précision

Dans la partie 1, nous avons montré comment classer une image en soumettant une machine à vecteurs de support linéaire à un apprentissage sur la sortie de 512 floats d’un réseau de neurones profond. Ce réseau de neurones profond a été préformé à des millions d’images. L’avant-dernière couche a été retournée en tant que vecteur de caractéristiques. Cette approche est rapide, car le réseau de neurones profond est utilisé tel quel. Toutefois, elle donne souvent de bons résultats.

Nous vous présentons maintenant plusieurs façons d’améliorer la précision du modèle de la partie 1. En particulier, nous affinons le réseau de neurones profond au lieu de le laisser fixe.

### <a name="dnn-refinement"></a>Affinement du réseau de neurones profond

Au lieu d’utiliser une machine à vecteurs de support, vous pouvez effectuer la classification directement dans le réseau de neurones. Pour ce faire, vous devez ajouter une nouvelle dernière couche au réseau de neurones profond préformé, qui accepte en entrée les 512 floats de l’avant-dernière couche. L’exécution de la classification dans le réseau de neurones profond présente l’avantage suivant : le réseau complet peut maintenant être reformé par rétropropagation. Cette approche conduit souvent à de meilleures précisions de classification par rapport à l’utilisation en l’état du réseau de neurones profond préformé. Toutefois, cela implique en contrepartie un temps d’apprentissage beaucoup plus long (même avec un GPU).

Pour former le réseau de neurones plutôt qu’une machine à vecteurs de support, changez la variable `classifier` dans `PARAMETERS.py` en remplaçant `svm` par `dnn`. Ensuite, comme indiqué dans la partie 1, vous devez réexécuter tous les scripts à l’exception de la préparation des données (étape 1) et de l’apprentissage de la machine à vecteurs de support (étape 3). L’affinement du réseau de neurones profond nécessite un GPU. En l’absence de GPU ou si le GPU est verrouillé (par exemple en raison d’une précédente exécution de CNTK), le script `2_refineDNN.py` génère une erreur. L’apprentissage du réseau de neurones profond peut entraîner une erreur d’insuffisance de mémoire sur certains GPU. Vous pouvez éviter cette situation en réduisant la taille du minilot (variable `cntk_mb_size` dans `PARAMETERS.py`).

Une fois l’apprentissage effectué, le modèle affiné est enregistré dans *DATA_DIR/proc/fashionTexture/cntk_refined.model*. Un tracé montre la façon dont les erreurs de classification d’apprentissage et de test changent durant l’apprentissage. Notez dans ce tracé que le taux d’erreurs sur le jeu d’apprentissage est beaucoup plus faible que sur le jeu de test. Vous pouvez réduire ce comportement de « surapprentissage », par exemple à l’aide d’une valeur plus élevée pour le taux d’abandon `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Comme vous pouvez le voir sur le tracé ci-dessous, la précision par affinement du réseau de neurones profond sur le jeu de données fourni est de 92,35 % contre 88,92 % auparavant (partie 1). En particulier, les images « avec des pois » s’améliorent de manière significative : l’aire sous la courbe ROC est de 0,98 avec affinement contre 0,94 auparavant. Comme nous utilisons un petit jeu de données, les précisions réelles liées à l’exécution du code sont différentes. Cet écart est dû à des effets stochastiques tels que la répartition aléatoire des images dans les jeux d’apprentissage et de test.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Suivi de l’historique des exécutions

Azure Machine Learning Workbench stocke l’historique de chaque exécution sur Azure pour permettre la comparaison de deux ou plusieurs exécutions, même quand elles sont espacées de plusieurs semaines. Ceci est expliqué en détail dans le [didacticiel Iris](tutorial-classifying-iris-part-2.md). Vous pouvez également le voir dans les captures d’écran suivantes où nous comparons deux exécutions du script `5_evaluate.py`, à l’aide de l’affinement du réseau de neurones profond via `classifier = "dnn"` (numéro d’exécution 148) ou de l’apprentissage de la machine à vecteurs de support via `classifier = "svm"` (numéro d’exécution 150).

Dans la première capture d’écran, l’affinement du réseau de neurones profond conduit à de meilleures précisions que l’apprentissage de la machine à vecteurs de support pour toutes les classes. La deuxième capture d’écran montre toutes les métriques qui font l’objet d’un suivi, ainsi que le classifieur. Ce suivi est effectué dans le script `5_evaluate.py` via l’appel de l’enregistreur d’événements d’Azure Machine Learning Workbench. De plus, le script enregistre également la courbe ROC et la matrice de confusion dans le dossier *outputs*. Ce dossier *outputs* est spécial dans la mesure où son contenu est également suivi par la fonctionnalité d’historique de Workbench. Ainsi, vous pouvez accéder aux fichiers de sortie à tout moment, indépendamment du fait que les copies locales aient été remplacées ou non.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>  
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>Réglage des paramètres
Comme c’est le cas pour la plupart des projets d’apprentissage automatique (« machine learning »), l’obtention de bons résultats pour un nouveau jeu de données nécessite un réglage minutieux des paramètres, ainsi qu’une évaluation pertinente des différents choix de conception. Pour vous faciliter la tâche, tous les paramètres importants sont spécifiés et une brève explication est fournie au même endroit, dans le fichier `PARAMETERS.py`.

Certaines des pistes d’amélioration les plus prometteuses sont :

- Qualité des données : vérifiez que les jeux d’apprentissage et de test sont de haute qualité. En d’autres termes, vérifiez que les images sont annotées correctement, que les images ambiguës sont supprimées (par exemple les vêtements comportant à la fois des rayures et des pois) et que les attributs sont mutuellement exclusifs (c’est-à-dire qu’ils sont choisis de telle sorte que chaque image appartient à un seul attribut).
- Si l’objet d’intérêt est petit sur l’image, les approches de classification d’images sont généralement moins efficaces. Dans ce cas, utilisez l’une des approches de détection d’objet décrites dans ce [didacticiel](https://github.com/Azure/ObjectDetectionUsingCntk).
- Affinement du réseau de neurones profond : le paramètre éventuellement le plus important pour obtenir un bon résultat est le taux d’apprentissage `rf_lrPerMb`. Si la précision du jeu d’apprentissage (première figure de la partie 2) n’est pas proche d’une valeur comprise entre 0 et 5 %, cela est très probablement dû à un mauvais taux d’apprentissage. Les autres paramètres commençant par `rf_` sont moins importants. En règle générale, l’erreur d’apprentissage doit diminuer de façon exponentielle et se rapprocher de 0 % après l’apprentissage.
- Résolution d’entrée : la résolution d’image par défaut est de 224 x 224 pixels. L’utilisation d’une résolution d’image plus élevée (paramètre : `rf_inputResoluton`), par exemple 448 x 448 pixels ou 896 x 896 pixels, améliore souvent la précision de façon importante mais ralentit l’affinement du réseau de neurones profond. **L’utilisation d’une résolution d’image plus élevée ne coûte quasiment rien et améliore presque toujours la précision**.
- Surapprentissage du réseau de neurones profond : évitez un écart important entre la précision de l’apprentissage et la précision des tests durant l’affinement du réseau de neurones profond (première figure de la partie 2). Vous pouvez réduire cet écart en utilisant des taux d’abandon (`rf_dropoutRate`) de 0,5 ou plus, et en augmentant le poids du régulateur `rf_l2RegWeight`. L’utilisation d’un taux d’abandon élevé peut être particulièrement utile si la résolution d’image d’entrée du réseau de neurones profond est élevée.
- Essayez d’utiliser des réseaux de neurones profonds encore plus profonds en changeant `rf_pretrainedModelFilename` et en remplaçant `ResNet_18.model` par `ResNet_34.model` ou `ResNet_50.model`. Non seulement le modèle Resnet-50 est plus profond, mais sa sortie de l’avant-dernière couche a une taille de 2 048 floats (contre 512 floats pour les modèles ResNet-18 et ResNet-34). Cette dimension accrue peut s’avérer particulièrement intéressante pour l’apprentissage d’un classifieur de machine à vecteurs de support.

## <a name="part-3---custom-dataset"></a>Partie 3 - Jeu de données personnalisé

Dans les parties 1 et 2, nous avons formé et évalué un modèle de classification d’images à partir d’images fournies représentant des motifs de vêtements pour le haut du corps. Nous montrons maintenant comment utiliser à la place un jeu de données personnalisé fourni par l’utilisateur. Ou, le cas échéant, comment générer et annoter un tel jeu de données à l’aide de l’outil Recherche d’images Bing.

### <a name="using-a-custom-dataset"></a>Utilisation d’un jeu de données personnalisé

Commençons par examiner la structure des dossiers de données pour les motifs des vêtements. Notez que toutes les images des différents attributs se trouvent dans les sous-dossiers respectifs *dotted*, *leopard et *striped* (pour « à pois », « léopard » et « à rayures ») sur *DATA_DIR/images/fashionTexture/*. Notez également que le nom du dossier image est présent également dans le fichier `PARAMETERS.py` :
```python
datasetName = "fashionTexture"
```

L’utilisation d’un jeu de données personnalisé consiste simplement à reproduire cette structure de dossiers, où toutes les images se trouvent dans des sous-dossiers en fonction de leurs attributs, et à copier ces sous-dossiers dans un nouveau répertoire spécifié par l’utilisateur *DATA_DIR/images/newDataSetName/*. Le seul changement nécessaire dans le code consiste à affecter à la variable `datasetName` la valeur *newDataSetName*. Les scripts 1 à 5 peuvent ensuite être exécutés dans l’ordre, et tous les fichiers intermédiaires sont écrits dans *DATA_DIR/proc/newDataSetName/*. Aucun autre changement dans le code n’est nécesssaire.

Il est important que chaque image puisse être affectée à un seul attribut. Par exemple, il est incorrect d’avoir des attributs pour « animal » et « léopard », car une image de « léopard » appartient aussi à « animal ». De même, il est préférable de supprimer les images ambiguës et donc difficiles à annoter.



### <a name="image-scraping-and-annotation"></a>Récupération et annotation d’images

La collecte d’un nombre suffisamment important d’images annotées pour l’apprentissage et les tests peut être une tâche difficile. Il est possible de surmonter ce problème en récupérant les images à partir d’Internet. Par exemple, consultez ci-dessous les résultats de la Recherche d’images Bing pour la requête *t-shirt rayé*. Comme prévu, la plupart des images sont en effet des t-shirts rayés. Les quelques images incorrectes ou ambiguës (par exemple dans la colonne 1, ligne 1, ou dans la colonne 3, ligne 2) peuvent être identifiées et supprimées facilement :
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

Pour générer un jeu de données volumineux et varié, vous devez utiliser plusieurs requêtes. Par exemple, vous pouvez synthétiser 7\*3 = 21 requêtes automatiquement à l’aide de toutes les combinaisons d’articles vestimentaires {blouse, sweat à capuche, pull-over, chandail, chemise, t-shirt, gilet} et des attributs {rayures, pois, léopard}. Le téléchargement des 50 premières images par requête produit alors un maximum de 21*50 = 1 050 images.

Au lieu de télécharger manuellement les images à partir de la Recherche d’images Bing, il est beaucoup plus simple d’utiliser à la place l’[API Recherche d’images Bing de Cognitive Services](https://www.microsoft.com/cognitive-services/bing-image-search-api), qui retourne un ensemble d’URL d’images en fonction d’une chaîne de requête.

Certaines des images téléchargées sont des doublons parfaits ou presque parfaits (par exemple, ils se distinguent uniquement par la résolution de l’image ou les artefacts jpg). Vous devez supprimer ces doublons pour éviter de répartir les mêmes images entre le jeu d’apprentissage et le jeu de test. Vous pouvez supprimer les images dupliquées à l’aide d’une approche basée sur le code de hachage. Il s’agit d’une opération en deux étapes : (i) dans un premier temps, la chaîne du code de hachage est calculée pour toutes les images ; (ii) au cours d’un second passage sur les images, seules sont conservées celles dont la chaîne du code de hachage n’a pas encore été vue. Toutes les autres images sont abandonnées. Nous avons trouvé l’approche `dhash` dans la bibliothèque Python `imagehash`. Nous avons décrit dans ce [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) la méthode à suivre pour l’utiliser correctement, avec la valeur 16 affectée au paramètre `hash_size`. Cela n’est pas grave si vous supprimez à tort certaines images non dupliquées, du moment que la majorité des doublons réels sont supprimés.





## <a name="conclusion"></a>Conclusion

Voici quelques points à retenir de cet exemple :
- Code permettant de former, évaluer et déployer des modèles de classification d’images.
- Images de démonstration fournies, mais facilement adaptables (une ligne à changer), pour vous permettre d’utiliser votre propre jeu de données d’images.
- Caractéristiques de pointe mises en œuvre pour l’apprentissage de modèles de haute précision en fonction d’un apprentissage par transfert.
- Développement de modèles interactifs à l’aide d’Azure Machine Learning Workbench et de Jupyter Notebook.


## <a name="references"></a>Références

[1] Alex Krizhevsky, Ilya Sutskever et Geoffrey E. Hinton, [_ImageNet Classification with Deep Convolutional Neural Networks_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming He, Xiangyu Zhang, Shaoqing Ren et Jian Sun, [_Deep Residual Learning for Image Recognition_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
