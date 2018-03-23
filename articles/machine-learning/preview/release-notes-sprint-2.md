---
title: Notes de publication d’Azure Machine Learning Workbench pour la version sprint 2 de décembre 2017
description: Ce document décrit en détail les mises à jour pour la version sprint 2 d’Azure Machine Learning
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: cb620040f8c82fd2015f93963d99739d38ec6db3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="sprint-2---december-2017"></a>Sprint 2 - décembre 2017 

#### <a name="version-number-01171115263"></a>Numéro de version : 0.1.1711.15263

>Voici comment vous pouvez [trouver le numéro de version](known-issues-and-troubleshooting-guide.md).

Bienvenue dans la troisième mise à jour d’Azure Machine Learning Workbench. Cette mise à jour inclut des améliorations dans l’application Workbench, l’interface de ligne de commande (CLI) et les services principaux. Merci beaucoup pour les sourires et les smileys mécontents que vous envoyez. La plupart des mises à jour suivantes sont les résultats directs de vos commentaires. 

## <a name="notable-new-features"></a>Nouvelles fonctionnalités notables
- [Prise en charge de SQL Server et Azure SQL DB comme source de données](data-prep-appendix2-supported-data-sources.md#types) 
- [Apprentissage approfondi sur Spark avec prise en charge GPU à l’aide de MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Tous les conteneurs AML sont compatibles avec les appareils Azure IoT Edge lors du déploiement (aucune étape supplémentaire requise)](http://aka.ms/aml-iot-edge-blog)
- Liste de modèles inscrits et vues de détails disponibles dans le portail Azure
- Accès aux cibles de calcul à l’aide de l’authentification par clé SSH en plus de l’accès par nom d’utilisateur/mot de passe. 
- Nouvel inspecteur de fréquence de modèle dans l’expérience de préparation des données. 

## <a name="detailed-updates"></a>Mises à jour détaillées
Vous trouverez ci-dessous une liste des mises à jour détaillées de chaque zone de composant d’Azure Machine Learning dans ce sprint.

### <a name="installer"></a>Programme d’installation
- Le programme d’installation peut être mis à jour automatiquement afin de prendre en charge les correctifs de bogues et les nouvelles fonctionnalités sans que l’utilisateur ne doive le réinstaller

### <a name="workbench-authentication"></a>Authentification Workbench
- Plusieurs correctifs du système d’authentification. Prévenez-nous si vous rencontrez toujours des problèmes de connexion.
- Modifications de l’interface utilisateur facilitant la recherche des paramètres du gestionnaire de proxy.

### <a name="workbench"></a>Workbench
- L’affichage de fichier en lecture seule a maintenant un arrière-plan bleu clair
- Bouton Modifier déplacé vers la droite pour faciliter son identification.
- Les formats de fichier « dsource », « dprep » et « ipynb » peuvent désormais être rendus au format texte brut
- Le Workbench inclut maintenant une nouvelle expérience d’édition qui guide les utilisateurs dans l’utilisation de l’IDE externe pour modifier des scripts et utiliser Workbench exclusivement pour modifier des types de fichiers ayant une expérience d’édition riche (par exemple, Notebooks, sources de données, packages de préparation des données)
- Le chargement de la liste des espaces de travail et des projets auxquels l’utilisateur a accès est maintenant beaucoup plus rapide

### <a name="data-preparation"></a>Préparation des données 
- Inspecteur de fréquence de modèle pour afficher les modèles de chaîne dans une colonne. Vous pouvez également filtrer vos données à l’aide de ces modèles. Une vue similaire à l’inspecteur de nombres de valeurs vous est présentée. La différence réside dans le fait que la fréquence de modèle indique le nombre de modèles uniques des données plutôt que le nombre de données uniques. Vous pouvez également inclure ou exclure toutes les lignes qui correspondent à un modèle donné.

![Image de l’inspecteur de fréquence de modèle sur un numéro de produit](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- Améliorations des performances lors de la recommandation de cas à réviser dans la colonne « Dériver des colonnes par exemple »

- [Prise en charge de SQL Server et Azure SQL DB comme source de données](data-prep-appendix2-supported-data-sources.md#types) 

![Image de la création d’une nouvelle source de données SQL Server](media/release-notes-sprint-2/sql-server-data-source.png)

- Vue d’aperçu rapide des nombres de lignes et de colonnes activée

![Image d’aperçu rapide du nombre de colonnes de ligne](media/release-notes-sprint-2/row-col-count.png)

- La préparation des données est activée dans tous les contextes de calcul
- Les sources de données qui utilisent une base de données SQL Server sont activées dans tous les contextes de calcul
- Les colonnes de grille de préparation des données peuvent être filtrées par type de données
- Résolution du problème de conversion de plusieurs colonnes à une date
- Résolution du problème tel que l’utilisateur pouvait sélectionner une colonne en tant que source dans « Dériver des colonnes par un exemple » s’il modifiait le nom de colonne de sortie en mode avancé.

### <a name="job-execution"></a>Exécution des tâches
Vous pouvez désormais créer et accéder à une cible de calcul de type remotedocker ou cluster à l’aide de l’authentification par clé SSH en procédant comme suit :
- Joindre une cible de calcul en utilisant la commande suivante dans la CLI

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] L’option -k (ou --use-azureml-ssh-key) dans la commande spécifie de générer et d’utiliser une clé SSH.

- Azure ML Workbench génère une clé publique et l’envoie à votre console. Connectez-vous à la cible de calcul avec le même nom d’utilisateur et ajoutez cette clé publique dans le fichier ~/.ssh/authorized_keys.

- Vous pouvez préparer cette cible de calcul et l’utiliser pour l’exécution, et Azure ML Workbench utilisera cette clé pour l’authentification.  

Pour plus d’informations sur la création de cibles de calcul, consultez [Configuration du service Azure Machine Learning - Expérimentation](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools pour IA
- Ajout de la prise en charge de [Visual Studio Tools pour AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interface de ligne de commande (CLI)
- Ajout de la commande `az ml datasource create` permettant de créer un fichier de source de données à partir de la ligne de commande

### <a name="model-management-and-operationalization"></a>Gestion des modèles et opérationnalisation
- [Tous les conteneurs AML sont compatibles avec les appareils Azure IoT Edge lors de l’opérationnalisation (aucune étape supplémentaire requise)](http://aka.ms/aml-iot-edge-blog) 
- Améliorations apportées aux messages d’erreur dans la CLI o16n
- Correctifs de bogues dans le portail de gestion des modèles UX  
- Casse cohérente pour les attributs de gestion des modèles dans la page de détails
- Délai d’expiration des appareils de calcul du score en temps réel défini sur 60 secondes
- Liste de modèles inscrits et vues de détails disponibles dans le portail Azure

![détails du modèle dans le portail](media/release-notes-sprint-2/model-list.jpg)

![vue d’ensemble du modèle dans le portail](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Apprentissage approfondi sur Spark avec [prise en charge GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Prise en charge de modèles Resource Manager pour un déploiement de ressource facile
- Prise en charge de l’écosystème SparklyR
- [Intégration AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Exemples de projets
- Exemples [Iris](https://github.com/Azure/MachineLearningSamples-Iris) et [MMLSpark](https://github.com/Azure/mmlspark) mis à jour avec la nouvelle version du Kit de développement logiciel (SDK) Azure ML

## <a name="breaking-changes"></a>DERNIÈRES MODIFICATIONS
- Promotion du commutateur `--type` dans `az ml computetarget attach` dans une sous-commande. 

    - `az ml computetarget attach --type remotedocker` est maintenant `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` est maintenant `az ml computetarget attach cluster`
