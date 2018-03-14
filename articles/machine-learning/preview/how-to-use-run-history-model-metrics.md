---
title: "Guide pratique pour utiliser l’historique des exécutions et les métriques de modèles dans Azure Machine Learning Workbench | Microsoft Docs"
description: "Guide de l’utilisation des fonctionnalités de l’historique des exécutions et des métriques de modèles d’Azure Machine Learning Workbench"
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 2d1bce6463ac7880fd9091b4f3f2cbb226ea516d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Guide pratique pour utiliser l’historique des exécutions et les métriques de modèles dans Azure Machine Learning Workbench

Azure Machine Learning Workbench prend en charge l’expérimentation de la science des données par le biais de ses fonctionnalités **Historique des exécutions** et **Métriques de modèles**.
L’**historique des exécutions** offre un moyen de suivre les sorties de vos expérimentations d’apprentissage automatique et permet d’en filtrer les résultats pour les comparer.
Les **métriques de modèles** peuvent être enregistrées à partir de n’importe quel point de vos scripts, pour suivre toutes les valeurs les plus importantes dans vos expérimentations de science des données.
Cet article décrit comment tirer parti de ces fonctionnalités pour augmenter la vitesse et la qualité de votre expérimentation de la science des données.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous devez :
* [Créer et installer Azure Machine Learning](quickstart-installation.md)
- [Créer un projet](quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Vue d’ensemble de l’API de journalisation Azure ML
L’[API de journalisation Azure ML](reference-logging-api.md) est disponible par le biais du module **azureml.logging** dans Python (installé avec Azure Machine Learning Workbench). Après l’importation de ce module, vous pouvez utiliser la méthode **get_azureml_logger** pour instancier un objet **enregistreur d’événements**.
Ensuite, vous pouvez utiliser la méthode **log** de l’enregistreur d’événements pour stocker les paires clé/valeur produites par vos scripts Python.
Actuellement, la journalisation des métriques de modèles de types scalaire et liste est prise en charge comme indiqué.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Il est facile d’utiliser l’enregistreur d’événements dans vos projets Azure Machine Learning Workbench, et cet article vous explique comment y parvenir.

## <a name="create-a-project-in-azure-ml-workbench"></a>Créer un projet dans Azure Machine Learning Workbench
Si vous n’avez pas déjà un projet, vous pouvez en créer un à partir du [Démarrage rapide d’installation et de création](quickstart-installation.md). À partir du **tableau de bord du projet**, vous pouvez ouvrir le script **iris_sklearn.py** (comme indiqué).

![accès à un script à partir de l’onglet Fichiers](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Vous pouvez utiliser ce script comme guide pour l’implémentation attendue de la journalisation des métriques de modèles dans Azure ML.

## <a name="parameterize-and-log-model-metrics-from-script"></a>Paramétrer et journaliser des métriques de modèles à partir d’un script
Dans le script **iris_sklearn.py**, le modèle attendu pour l’importation et la construction de l’enregistreur d’événements dans Python peut se réduire aux lignes de code suivantes.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

Une fois créé, vous pouvez appeler la méthode **log** avec n’importe quelle paire nom/valeur.

Une fois le développement terminé, il est souvent utile de paramétrer des scripts afin que les valeurs puissent être passées par le biais de la ligne de commande.
L’exemple ci-dessous montre comment accepter des paramètres de ligne de commande (le cas échéant) à l’aide de bibliothèques Python standard.
Ce script accepte un seul paramètre pour le taux de régularisation (*reg*) utilisé pour ajuster un modèle de classification dans le but d’augmenter la *précision* sans surajustement.
Ces variables sont alors enregistrées en tant que *taux de régularisation* et *précision* pour que le modèle présentant des résultats optimaux soit facilement identifié.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Faire passer vos scripts par ces étapes leur permet d’utiliser l’**historique des exécutions** de façon optimale.

## <a name="launch-runs-from-project-dashboard"></a>Lancer des exécutions à partir du tableau de bord du projet
En revenant au **tableau de bord du projet**, vous pouvez lancer une **exécution suivie** en sélectionnant le script **iris_sklearn.py** et en entrant le paramètre de **taux de régularisation** dans la zone d’édition **Arguments**.

![entrée des paramètres et lancement d’exécutions](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Étant donné que le lancement d’exécutions suivies ne bloque pas Azure Machine Learning Workbench, vous pouvez en lancer plusieurs en parallèle.
L’état de chaque exécution suivie est visible dans le **panneau des travaux** comme indiqué.

![exécutions suivies dans le panneau des travaux](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Ainsi, l’utilisation des ressources est optimale et n’exige pas que chaque travail s’exécute l’un après l’autre.

## <a name="view-results-in-run-history"></a>Afficher les résultats dans l’historique des exécutions
La progression et les résultats des exécutions suivies sont disponibles à des fins d’analyse dans l’**historique des exécutions** Azure Machine Learning Workbench.
L’**historique des exécutions** fournit trois vues distinctes :
- tableau de bord
- Détails
- Opérateurs de comparaison

La vue **Tableau de bord** présente les données de toutes les exécutions d’un script donné, rendues sous forme à la fois graphique et tabulaire.
La vue **Détails** présente toutes les données générées à partir d’une exécution spécifique d’un script donné, notamment les métriques journalisées et les fichiers de sortie (comme les tracés rendus). La vue **Comparaison** permet de présenter les résultats de deux ou trois exécutions côte à côte, en incluant également les métriques journalisées et les fichiers de sortie.

Pour les huit exécutions suivies de **iris_sklearn.py**, des valeurs ont été journalisées pour le paramètre du **taux de régularisation** et le résultat de la **précision** afin d’illustrer la manière d’utiliser les vues de l’historique des exécutions.

### <a name="run-history-dashboard"></a>Tableau de bord de l’historique des exécutions
Les résultats des huit exécutions sont visibles dans le **tableau de bord de l’historique des exécutions**.
En tant donné que **iris_sklearn.py** journalise le *taux de régularisation* et la *précision*, le **tableau de bord de l’historique des exécutions** présente par défaut des graphiques pour ces valeurs.

![tableau de bord de l’historique des exécutions](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

Le **tableau de bord de l’historique des exécutions** peut être personnalisé afin que les valeurs enregistrées apparaissent également dans la grille.  En cliquant sur l’icône de **personnalisation**, la boîte de dialogue de **personnalisation de la vue sous forme de liste** s’affiche.

![personnalisation de la grille du tableau de bord de l’historique des exécutions](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Toutes les valeurs enregistrées pendant les exécutions suivies sont disponibles à l’affichage et le fait de sélectionner le **taux de régularisation** et la **précision** les ajoute à la grille.

![valeurs enregistrées dans la grille personnalisée](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Il est facile de trouver les exécutions intéressantes en pointant le curseur sur des points dans les graphiques.  Dans cet exemple, l’exécution 7 a produit une bonne précision avec une durée faible.

![recherche d’une exécution intéressante](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

En cliquant sur un point associé à l’exécution 7 dans un graphique ou sur le lien vers l’exécution 7 dans la grille, les **détails de l’historique des exécutions** s’affiche.

### <a name="run-history-details"></a>Détails de l’historique des exécutions
À partir de cette vue, les résultats complets de l’exécution 7, ainsi que tous les artefacts produits par l’exécution 7 sont présentés.

![détails de l’historique des exécutions](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

La vue des **détails de l’historique des exécutions** offre également la possibilité de **télécharger** tous les fichiers écrits dans le dossier **./outputs** (ces fichiers sont sauvegardés dans le stockage cloud Azure Machine Learning Workbench pour l’historique des exécutions, ce qui fait l’objet d’un autre article).

Enfin, les **détails de l’historique des exécutions** propose un moyen de rétablir votre projet à son état au moment de l’exécution.
En cliquant sur le bouton **Restaurer**, une boîte de dialogue de confirmation s’affiche, comme indiqué.

![confirmation du rétablissement de l’exécution](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

Si vous confirmez, les fichiers risquent d’être remplacés ou supprimés, alors utilisez cette fonctionnalité avec précaution.

### <a name="run-history-comparison"></a>Comparaison de l’historique des exécutions
La sélection de deux ou trois exécutions dans le **tableau de bord de l’historique des exécutions** et le fait de cliquer sur **Comparer** vous amènent à la vue **comparative de l’historique des exécutions**.
Vous pouvez également cliquer sur **Comparer** et sélectionner une exécution dans la vue des **détails de l’historique des exécutions** pour accéder à la vue **comparative de l’historique des exécutions**.
Dans les deux cas, la vue **comparative de l’historique des exécutions** offre un moyen de présenter côte à côte les résultats et artefacts enregistrés de deux ou trois exécutions.

![comparaison de l’historique des exécutions](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Cette vue s’avère particulièrement utile pour comparer des tracés, mais en général, vous pouvez y comparer toutes les propriétés des exécutions.

### <a name="command-line-interface"></a>Interface de ligne de commande
Azure Machine Learning Workbench permet également d’accéder à l’historique des exécutions par le biais de son **interface de ligne de commande**.
Pour accéder à la l’**interface de ligne de commande**, cliquez sur le menu **Ouvrir l’invite de commandes**, comme indiqué.

![ouvrir l’invite de commandes](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Les commandes disponibles pour l’historique des exécutions sont accessibles par le biais de la commande `az ml history`, avec une aide en ligne en ajoutant l’indicateur `-h`.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Ces commandes fournissent les mêmes fonctionnalités et retournent les mêmes données que celles indiquées par les **vues de l’historique des exécutions**.
Par exemple, les résultats de la dernière exécution peuvent s’afficher sous la forme d’un objet JSON.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
En outre, la liste de toutes les exécutions peut s’afficher dans un format tabulaire.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
L’**interface de ligne de commande** est un autre moyen d’accéder à la puissance d’Azure Machine Learning Workbench.

## <a name="next-steps"></a>Étapes suivantes
Ces fonctionnalités sont disponibles pour faciliter le processus d’expérimentation de la science des données.
Nous espérons que vous les trouverez utiles et serions ravis de recueillir vos commentaires à leur sujet.
Cette implémentation n’est qu’un début et nous avons déjà planifié de nombreuses améliorations.
Nous avons hâte de les appliquer progressivement à Azure Machine Learning Workbench. 
