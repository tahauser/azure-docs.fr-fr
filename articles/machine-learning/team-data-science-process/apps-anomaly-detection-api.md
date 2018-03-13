---
title: "API de détection des anomalies Machine Learning | Microsoft Docs"
description: "L’API de détection des anomalies est un exemple d’API généré avec Microsoft Azure Machine Learning. Elle détecte des anomalies dans les données de séries chronologiques présentant des valeurs numériques qui sont réparties uniformément dans le temps."
services: machine-learning
documentationcenter: 
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok;rotimpe
ms.openlocfilehash: e2adfffa00a726fe2c452c25dd777ef054319b04
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="machine-learning-anomaly-detection-api"></a>API de détection des anomalies Machine Learning
## <a name="overview"></a>Vue d'ensemble
L’[API de détection des anomalies](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) est un exemple d’API généré avec Microsoft Azure Machine Learning. Elle détecte des anomalies dans les données de séries chronologiques présentant des valeurs numériques qui sont réparties uniformément dans le temps.

Cette API peut détecter les types suivants de schémas anormaux dans les données de séries chronologiques :

* **Tendances positives et négatives :**par exemple, pendant l’analyse de l’utilisation de la mémoire de l’infrastructure informatique, une tendance à la hausse peut être digne d’intérêt, car elle est susceptible d’être le signe d’une fuite de mémoire.
* **Changements de la plage de valeurs dynamiques :**par exemple, pendant l’analyse des exceptions déclenchées par un service cloud, tout changement de la plage de valeurs dynamiques peut indiquer une instabilité de l’intégrité du service.
* **Pics et creux :**par exemple, quand vous analysez le nombre d’échecs de connexion pour un service ou le nombre de validations dans un site de commerce électronique, les pics ou creux d’activité peuvent indiquer un comportement anormal.

Ces détecteurs Machine Learning effectuent le suivi de tels changements de valeurs au fil du temps, signalant les changements en continu au sein de leurs valeurs en tant que résultats d’anomalies. Ils ne nécessitent pas de réglage du seuil ad hoc et leurs notations peuvent être utilisées pour contrôler le taux de faux positifs. L’API de détection des anomalies est utile dans plusieurs scénarios, tels que la surveillance des services via le suivi des indicateurs de performance clés au fil du temps, la surveillance de l’utilisation via des mesures comme le nombre de recherches et le nombre de clics, la surveillance des performances via des compteurs comme la mémoire, les processeurs, le nombre de lectures de fichiers, etc. au fil du temps.

L’offre de détection des anomalies inclut des outils utiles pour bien commencer.

* [L’application web](http://anomalydetection-aml.azurewebsites.net/) vous aide à évaluer et à visualiser les résultats de l’exécution de l’API de détection des anomalies sur vos données.

> [!NOTE]
> Essayez la **solution IT Anomaly Insights** basée sur [cette API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).
> 
> Pour déployer cette solution de bout en bout dans votre abonnement Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **ici >**</a>
> 
>

## <a name="api-deployment"></a>Déploiement de l’API
Pour pouvoir utiliser l’API, vous devez la déployer dans votre abonnement Azure où elle sera hébergée comme un service web Azure Machine Learning.  Vous pouvez effectuer cette opération dans la [galerie Azure AI](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Cette action déploie deux services Web AzureML (et leurs ressources associées) dans votre abonnement Azure : un pour la détection d’anomalies avec détection de saisonnalité et un autre sans détection de saisonnalité.  Une fois le déploiement terminé, vous serez en mesure de gérer vos API à partir de la page [services web AzureML](https://services.azureml.net/webservices/).  Sur cette page, vous pourrez trouver l’emplacement de vos points de terminaison, de vos clés API, ainsi que les exemples de code pour appeler l’API.  Des instructions plus détaillées sont disponibles [ici](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Mise à l’échelle de l’API
Par défaut, votre déploiement dispose d’un plan de facturation gratuit de développement/test qui comprend 1 000 transactions par mois et 2 heures de calcul par mois.  Vous pouvez mettre à niveau votre plan en fonction de vos besoins.  Vous pouvez retrouver des informations sur la tarification des différents plans [ici](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) sous « Tarification des API web de production ».

## <a name="managing-aml-plans"></a>Gestion des plans AML 
Vous pouvez gérer votre plan de facturation [ici](https://services.azureml.net/plans/).  Le nom du plan doit être basé sur le nom du groupe de ressources que vous avez choisi lors du déploiement de l’API, plus une chaîne unique à votre abonnement.  Vous pouvez retrouver des instructions sur la mise à niveau de votre plan [ici](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) dans la section « Gestion des plans de facturation ».

## <a name="api-definition"></a>Définition de l’API
Le service web fournit des API REST sur HTTPS qui peuvent être utilisées de différentes façons, notamment une application web ou mobile, R, Python, Excel, etc.  L’envoi des données de séries chronologiques à ce service se fait via un appel d’API REST ; celui-ci exécute une combinaison des trois types de détection d’anomalie décrits ci-dessous.

## <a name="calling-the-api"></a>Appel de l’API
Pour appeler l’API, vous devez connaître l’emplacement du point de terminaison et la clé d’API.  Ces deux éléments, ainsi que l’exemple de code d’appel de l’API, sont disponibles sur la page [services web AzureML](https://services.azureml.net/webservices/).  Accédez à l’API souhaitée, puis cliquez sur l’onglet « Utiliser » pour les trouver.  Notez que vous pouvez appeler l’API comme API Swagger (par exemple, avec le paramètre d’URL `format=swagger`) ou comme API non-Swagger (c’est-à-dire sans le paramètre d’URL `format`).  L’exemple de code utilise le format Swagger.  Voici un exemple de demande et de réponse au format non-Swagger.  Ces exemples sont relatifs au point de terminaison de saisonnalité.  Le point de terminaison sans saisonnalité est similaire.

### <a name="sample-request-body"></a>Exemple de corps de la demande
La demande contient deux objets : `Inputs` et `GlobalParameters`.  Dans l’exemple de demande ci-dessous, certains paramètres sont envoyés de façon explicite tandis que d’autres ne le sont pas (faites défiler pour voir la liste complète des paramètres de chaque point de terminaison).  Les paramètres qui ne sont pas envoyés de façon explicite dans la demande utilisent les valeurs par défaut indiquées ci-dessous.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Exemple de réponse
Notez que, pour pouvoir voir le champ `ColumnNames`, vous devez inclure `details=true` comme paramètre d’URL dans votre demande.  Consultez les tableaux ci-dessous pour connaître la signification de chacun de ces champs.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>API Score
L’API Score est utilisée pour la détection des anomalies dans les données de séries chronologiques non saisonnières. L’API exécute un certain nombre de détecteurs d’anomalies sur les données et renvoie leurs résultats d’anomalies. La figure ci-dessous illustre un exemple d’anomalies détectables par l’API Score. Cette série chronologique inclut 2 changements de niveau distincts, ainsi que 3 pics. Les points rouges indiquent l’heure à laquelle le changement de niveau a été détecté, tandis que les points noirs signalent les pics détectés.
![API Score][1]

### <a name="detectors"></a>Détecteurs
L’API de détection des anomalies prend en charge les détecteurs de 3 grandes catégories. Le tableau suivant contient des informations détaillées sur les paramètres d’entrée spécifiques et les sorties de chaque détecteur.

| Catégorie de détecteurs | Détecteur | Description | Paramètres d’entrée | Outputs |
| --- | --- | --- | --- | --- |
| Détecteurs de pics |Détecteurs TSpike |Détecter des pics et des creux en fonction de l’écart des valeurs par rapport aux premier et troisième quartiles |*tspikedetector.Sensitivity :* prend une valeur d’entier dans la plage de 1 à 10. Par défaut : 3. Des valeurs supérieures interceptent plus de valeurs extrêmes, rendant la détection moins sensible |TSpike : valeurs binaires (1 si un pic/creux est détecté, 0 dans le cas contraire) |
| Détecteurs de pics | Détecteur ZSpike |Détecter des pics et des creux en fonction de l’écart des points de données par rapport à leur moyenne |*zspikedetector.sensitivity :* prend une valeur d’entier dans la plage de 1 à 10. Par défaut : 3. Des valeurs supérieures interceptent plus de valeurs extrêmes, rendant la détection moins sensible |ZSpike : valeurs binaires (1 si un pic/creux est détecté, 0 dans le cas contraire) | |
| Détecteur de tendances lentes |Détecteur de tendances lentes |Détecter les tendances positives lentes suivant la sensibilité définie |*trenddetector.sensitivity :* seuil pour le résultat du détecteur (par défaut : 3.25, une valeur comprise entre 3.25 et 5 est raisonnable ; plus la valeur est élevée, moins la sensibilité est importante) |tscore : nombre flottant représentant le résultat d’anomalies pour une tendance |
| Détecteurs de changements de niveau | Détecteur de changements de niveau bidirectionnels |Détecter les changements de niveau vers le haut et vers le bas suivant la sensibilité définie |*bileveldetector.sensitivity :* seuil pour le résultat du détecteur (par défaut : 3.25, une valeur comprise entre 3.25 et 5 est raisonnable ; plus la valeur est élevée, moins la sensibilité est importante) |rscore : nombre flottant représentant le résultat d’anomalies pour un changement de niveau vers le haut et vers le bas | |

### <a name="parameters"></a>parameters
Le tableau ci-dessous contient des informations plus détaillées sur ces paramètres d’entrée :

| Paramètres d’entrée | Description | Paramètre par défaut | type | Plage valide | Plage suggérée |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |Historique (en nombre de points de données) utilisé pour le calcul du résultat d’anomalies |500 |integer |10-2000 |Dépend des séries chronologiques |
| detectors.spikesdips | Détection des pics uniquement, des creux uniquement ou des deux |Les deux |enumerated |Les deux, pics, creux |Les deux |
| bileveldetector.sensitivity |Sensibilité du détecteur de changements de niveau bidirectionnels. |3.25 |double |Aucune |3.25-5 (plus la valeur est basse, plus la sensibilité est importante) |
| trenddetector.sensitivity |Sensibilité du détecteur de tendances positives |3.25 |double |Aucune |3.25-5 (plus la valeur est basse, plus la sensibilité est importante) |
| tspikedetector.sensitivity |Sensibilité du détecteur TSpike |3 |integer |1-10 |3-5 (plus la valeur est basse, plus la sensibilité est importante) |
| zspikedetector.sensitivity |Sensibilité du détecteur ZSpike |3 |integer |1-10 |3-5 (plus la valeur est basse, plus la sensibilité est importante) |
| postprocess.tailRows |Nombre de points de données récents à conserver dans les résultats de sortie |0 |integer |0 (conserver tous les points de données) ou spécifier le nombre de points à conserver dans les résultats |Non applicable |

### <a name="output"></a>Sortie
L’API exécute tous les détecteurs sur vos données de séries chronologiques et renvoie les résultats d’anomalies et les indicateurs de pics binaires pour chaque point dans le temps. Le tableau ci-dessous répertorie les sorties de l’API. 

| Outputs | Description |
| --- | --- |
| Temps |Horodatages issus des données brutes ou des données agrégées (et/ou) imputées si l’imputation des données agrégées (et/ou) manquantes est appliquée. |
| Données |Valeurs issues des données brutes ou des données agrégées (et/ou) imputées si l’imputation des données agrégées (et/ou) manquantes est appliquée. |
| TSpike |Indicateur binaire signalant si un pic est détecté par le détecteur TSpike |
| ZSpike |Indicateur binaire signalant si un pic est détecté par le détecteur ZSpike |
| rpscore |Nombre flottant représentant le résultat d’anomalies pour un changement de niveau bidirectionnel |
| rpalert |Valeur de 1/0 indiquant s’il y a une anomalie de changement de niveau bidirectionnel vers le haut en fonction de la sensibilité d’entrée |
| tscore |Nombre flottant représentant le résultat d’anomalies pour une tendance positive |
| talert |Valeur de 1/0 indiquant s’il y a une anomalie de tendance positive en fonction de la sensibilité d’entrée |

## <a name="scorewithseasonality-api"></a>API ScoreWithSeasonality
L’API ScoreWithSeasonality est utilisée pour la détection des anomalies dans les séries chronologiques qui présentent des schémas saisonniers. Cette API est utile pour détecter les écarts dans les schémas saisonniers.  
La figure suivante illustre un exemple d’anomalies détectées dans une série chronologique saisonnière. Cette série chronologique présente 1 pic (le 1er point noir), 2 creux (le 2e point noir et celui à la fin) et 1 changement de niveau (le point rouge). Notez que le creux au milieu de la série chronologique et le changement de niveau ne sont visibles qu’une fois les composantes saisonnières supprimées de la série.
![API Saisonnalité][2]

### <a name="detectors"></a>Détecteurs
Les détecteurs du point de terminaison du caractère saisonnier sont similaires à ceux du point de terminaison sans saisonnalité, mais avec des noms de paramètres légèrement différents (répertoriés ci-après).

### <a name="parameters"></a>parameters

Le tableau ci-dessous contient des informations plus détaillées sur ces paramètres d’entrée :

| Paramètres d’entrée | Description | Paramètre par défaut | type | Plage valide | Plage suggérée |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Intervalle d’agrégation en secondes pour l’agrégation de séries chronologiques d’entrée |0 (aucune agrégation n’est effectuée) |integer |0 : ignorer l’agrégation, > 0 autrement |5 minutes à 1 jour, dépend des séries chronologiques |
| preprocess.aggregationFunc |Fonction utilisée pour agréger les données dans l’intervalle d’agrégation spécifié |mean |enumerated |mean, sum, length |Non applicable |
| preprocess.replaceMissing |Valeurs utilisées pour imputer les données manquantes |lkv (dernière valeur connue) |enumerated |zero, lkv, mean |Non applicable |
| detectors.historyWindow |Historique (en nombre de points de données) utilisé pour le calcul du résultat d’anomalies |500 |integer |10-2000 |Dépend des séries chronologiques |
| detectors.spikesdips | Détection des pics uniquement, des creux uniquement ou des deux |Les deux |enumerated |Les deux, pics, creux |Les deux |
| bileveldetector.sensitivity |Sensibilité du détecteur de changements de niveau bidirectionnels. |3.25 |double |Aucune |3.25-5 (plus la valeur est basse, plus la sensibilité est importante) |
| postrenddetector.sensitivity |Sensibilité du détecteur de tendances positives |3.25 |double |Aucune |3.25-5 (plus la valeur est basse, plus la sensibilité est importante) |
| negtrenddetector.sensitivity |Sensibilité du détecteur de tendances négatives. |3.25 |double |Aucune |3.25-5 (plus la valeur est basse, plus la sensibilité est importante) |
| tspikedetector.sensitivity |Sensibilité du détecteur TSpike |3 |integer |1-10 |3-5 (plus la valeur est basse, plus la sensibilité est importante) |
| zspikedetector.sensitivity |Sensibilité du détecteur ZSpike |3 |integer |1-10 |3-5 (plus la valeur est basse, plus la sensibilité est importante) |
| seasonality.enable |Exécution de l’analyse de saisonnalité |true |booléenne |true, false |Dépend des séries chronologiques |
| seasonality.numSeasonality |Nombre maximal de cycles périodiques à détecter |1 |integer |1, 2 |1-2 |
| seasonality.transform |Suppression des composantes de tendances (et) saisonnières avant l’exécution de la détection des anomalies |deseason |enumerated |none, deseason, deseasontrend |Non applicable |
| postprocess.tailRows |Nombre de points de données récents à conserver dans les résultats de sortie |0 |integer |0 (conserver tous les points de données) ou spécifier le nombre de points à conserver dans les résultats |Non applicable |

### <a name="output"></a>Sortie
L’API exécute tous les détecteurs sur vos données de séries chronologiques et renvoie les résultats d’anomalies et les indicateurs de pics binaires pour chaque point dans le temps. Le tableau ci-dessous répertorie les sorties de l’API. 

| Outputs | Description |
| --- | --- |
| Temps |Horodatages issus des données brutes ou des données agrégées (et/ou) imputées si l’imputation des données agrégées (et/ou) manquantes est appliquée. |
| OriginalData |Valeurs issues des données brutes ou des données agrégées (et/ou) imputées si l’imputation des données agrégées (et/ou) manquantes est appliquée. |
| ProcessedData |Soit :  <ul><li>série chronologique ajustée de façon saisonnière si un caractère saisonnier important a été détectée et si l’option deseason est sélectionnée ;</li><li>série chronologique redressée et ajustée de façon saisonnière si un caractère saisonnier important a été détectée et si l’option deseasontrend est sélectionnée ;</li><li>autrement, identique à OriginalData.</li> |
| TSpike |Indicateur binaire signalant si un pic est détecté par le détecteur TSpike |
| ZSpike |Indicateur binaire signalant si un pic est détecté par le détecteur ZSpike |
| BiLevelChangeScore |Nombre flottant représentant le résultat d’anomalies pour un changement de niveau |
| BiLevelChangeAlert |Valeur 1/0 indiquant s’il y a une anomalie de changement de niveau en fonction de la sensibilité d’entrée |
| PosTrendScore |Nombre flottant représentant le résultat d’anomalies pour une tendance positive |
| PosTrendAlert |Valeur de 1/0 indiquant s’il y a une anomalie de tendance positive en fonction de la sensibilité d’entrée |
| NegTrendScore |Nombre flottant représentant le résultat d’anomalies pour une tendance négative |
| NegTrendAlert |Valeur de 1/0 indiquant s’il y a une anomalie de tendance négative en fonction de la sensibilité d’entrée |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

