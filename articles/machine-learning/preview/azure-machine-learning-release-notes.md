---
title: Nouveautés dans Azure Machine Learning
description: Ce document décrit en détail les mises à jour d’Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: ac08baa6f478926a2c8dadd366049e9506272366
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Nouveautés dans Azure Machine Learning

Dans cet article, découvrez les nouvelles fonctionnalités et les problèmes connus dans [Azure Machine Learning Services](overview-what-is-azure-ml.md). 

## <a name="2018-01-sprint-3"></a>2018-01 (Sprint 3) 
**Numéro de version** : 0.1.1712.18263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Recherchez votre version](known-issues-and-troubleshooting-guide.md))

Voici les mises à jour et les améliorations de cette version sprint. La plupart de ces mises à jour sont les résultats directs des commentaires des utilisateurs. 


Vous trouverez ci-dessous une liste des mises à jour détaillées de chaque zone de composant d’Azure Machine Learning dans ce sprint.

- Les mises à jour de la pile d’authentification obligent à sélectionner un compte et un identifiant au démarrage

### <a name="workbench"></a>Workbench
- Possibilité d’installer/de désinstaller l’application à partir de l’assistant d’ajout/suppression de programmes
- Les mises à jour de la pile d’authentification obligent à sélectionner un compte et un identifiant au démarrage
- Amélioration de l’expérience d’authentification unique (SSO) sur Windows
- Les utilisateurs qui appartiennent à plusieurs clients avec différentes informations d’identification pourront désormais se connecter à Workbench

### <a name="ui"></a>Interface utilisateur
- Améliorations générales et résolution de bogues

### <a name="notebooks"></a>Blocs-notes
- Améliorations générales et résolution de bogues

### <a name="data-preparation"></a>Préparation des données 
- Amélioration des suggestions automatiques lors de l’exécution de transformations Par l’exemple
- Amélioration de l’algorithme pour l’inspecteur de fréquence de motifs
- Possibilité d’envoyer des exemples de commentaires et de données lors de l’exécution de transformations Par l’exemple ![Image du lien d’envoi de commentaires lors de la dérivation d’une colonne](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Améliorations apportées au runtime Spark
- Scala a remplacé Pyspark
- Élimination de l’incapacité à fermer les données non applicables à Time Series Inspector 
- Résolution du blocage de l’heure d’exécution de la préparation des données pour HDI

### <a name="model-management-cli-updates"></a>Mises à jour de l’interface de commande de gestion des modèles 
  - La propriété de l’abonnement n’est plus nécessaire pour l’approvisionnement des ressources. L’accès collaborateur au groupe de ressources est suffisant pour configurer l’environnement de déploiement.
  - Autorisation de configuration de l’environnement local pour les abonnements gratuits 

## <a name="2017-12-sprint-2-qfe"></a>2017-12 (Sprint 2 QFE) 
**Numéro de version** : 0.1.1711.15323  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Recherchez votre version](known-issues-and-troubleshooting-guide.md))

Il s’agit de la version QFE (Quick Fix Engineering), une version mineure. Elle résout plusieurs problèmes de télémétrie et aide l’équipe de produit à mieux comprendre comment le produit est utilisé, et par conséquent à en améliorer l’expérience. 

En outre, il existe deux mises à jour importantes :

- Correction d’un bogue dans la préparation des données qui empêchait l’affichage de l’inspecteur de série chronologique dans les packages de préparation des données.
- Dans l’outil en ligne de commande, vous n’avez plus besoin d’être propriétaire d’un abonnement Azure pour provisionner des clusters de calcul ACS Machine Learning. 

## <a name="2017-12-sprint-2"></a>2017-12 (Sprint 2)
**Numéro de version** : 0.1.1711.15263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Recherchez votre version](known-issues-and-troubleshooting-guide.md))


Bienvenue dans la troisième mise à jour d’Azure Machine Learning. Cette mise à jour inclut des améliorations dans l’application Workbench, l’interface de ligne de commande (CLI) et les services principaux. Merci beaucoup pour les sourires et les smileys mécontents que vous envoyez. La plupart des mises à jour suivantes sont les résultats directs de vos commentaires. 

**Nouvelles fonctionnalités notables**
- [Prise en charge de SQL Server et Azure SQL DB comme source de données](data-prep-appendix2-supported-data-sources.md#types) 
- [Apprentissage approfondi sur Spark avec prise en charge GPU à l’aide de MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Tous les conteneurs AML sont compatibles avec les appareils Azure IoT Edge lors du déploiement (aucune étape supplémentaire requise)](http://aka.ms/aml-iot-edge-blog)
- Liste de modèles inscrits et vues de détails disponibles dans le portail Azure
- Accès aux cibles de calcul à l’aide de l’authentification par clé SSH en plus de l’accès par nom d’utilisateur/mot de passe. 
- Nouvel inspecteur de fréquence de modèle dans l’expérience de préparation des données. 

**Mises à jour détaillées** Vous trouverez ci-dessous une liste des mises à jour détaillées de chaque zone de composant d’Azure Machine Learning dans ce sprint.

### <a name="installer"></a>Programme d’installation
- Le programme d’installation peut être mis à jour automatiquement afin de prendre en charge les correctifs de bogues et les nouvelles fonctionnalités sans que l’utilisateur ne doive le réinstaller

### <a name="workbench-authentication"></a>Authentification Workbench
- Plusieurs correctifs du système d’authentification. Prévenez-nous si vous rencontrez toujours des problèmes de connexion.
- Modifications de l’interface utilisateur facilitant la recherche des paramètres du gestionnaire de proxy.

### <a name="workbench"></a>Workbench
- L’affichage de fichier en lecture seule a maintenant un arrière-plan bleu clair
- Bouton Modifier déplacé vers la droite pour faciliter son identification.
- Les formats de fichier « dsource », « dprep » et « ipynb » peuvent désormais être rendus au format texte brut
- Le Workbench inclut maintenant une nouvelle expérience d’édition qui guide les utilisateurs dans l’utilisation d’IDE externes pour modifier des scripts et utiliser Workbench exclusivement pour modifier des types de fichiers ayant une expérience d’édition riche (par exemple, Notebooks, sources de données, packages de préparation des données)
- Le chargement de la liste des espaces de travail et des projets auxquels l’utilisateur a accès est maintenant beaucoup plus rapide

### <a name="data-preparation"></a>Préparation des données 
- Inspecteur de fréquence de modèle pour afficher les modèles de chaîne dans une colonne. Vous pouvez également filtrer vos données à l’aide de ces modèles. Une vue similaire à l’inspecteur de nombres de valeurs vous est présentée. La différence réside dans le fait que la fréquence de modèle indique le nombre de modèles uniques des données plutôt que le nombre de données uniques. Vous pouvez également inclure ou exclure toutes les lignes qui correspondent à un modèle donné.

![Image de l’inspecteur de fréquence de modèle sur un numéro de produit](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Améliorations des performances lors de la recommandation de cas à réviser dans la colonne « Dériver des colonnes par exemple »

- [Prise en charge de SQL Server et Azure SQL DB comme source de données](data-prep-appendix2-supported-data-sources.md#types) 

![Image de la création d’une nouvelle source de données SQL Server](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Vue d’aperçu rapide des nombres de lignes et de colonnes activée

![Image d’aperçu rapide du nombre de colonnes de ligne](media/azure-machine-learning-release-notes/row-col-count.png)

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
>[!NOTE]
>L’option -k (ou --use-azureml-ssh-key) dans la commande spécifie de générer et d’utiliser une clé SSH.

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

![détails du modèle dans le portail](media/azure-machine-learning-release-notes/model-list.jpg)

![vue d’ensemble du modèle dans le portail](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Apprentissage approfondi sur Spark avec [prise en charge GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Prise en charge de modèles Resource Manager pour un déploiement de ressource facile
- Prise en charge de l’écosystème SparklyR
- [Intégration AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Exemples de projets
- Exemples [Iris](https://github.com/Azure/MachineLearningSamples-Iris) et [MMLSpark](https://github.com/Azure/mmlspark) mis à jour avec la nouvelle version du Kit de développement logiciel (SDK) Azure ML

### <a name="breaking-changes"></a>Dernières modifications
- Promotion du commutateur `--type` dans `az ml computetarget attach` dans une sous-commande. 

    - `az ml computetarget attach --type remotedocker` est maintenant `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` est maintenant `az ml computetarget attach cluster`

## <a name="2017-11-sprint-1"></a>2017-11 (Sprint 1) 
**Numéro de version** : 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Recherchez votre version](known-issues-and-troubleshooting-guide.md))

Dans cette version, nous avons amélioré la sécurité, la stabilité et la maintenabilité de l’application Workbench, de l’interface CLI et de la couche des services backend. Merci beaucoup pour les sourires et les smileys mécontents que vous nous envoyez. La plupart des mises à jour ci-dessous sont les résultats directs de vos commentaires. Continuez à en envoyer !

### <a name="notable-new-features"></a>Nouvelles fonctionnalités notables
- Azure ML est maintenant disponible dans deux nouvelles régions Azure : l’**Europe de l’Ouest** et le **Sud-Est asiatique**. Elles rejoignent les autres régions déjà présentes, **États-Unis de l’Est 2**, **Centre-Ouest des États-Unis** et **Est de l’Australie**, ce qui porte à cinq le nombre total de régions déployées.
- Nous avons activé la coloration syntaxique du code Python dans l’application Workbench pour faciliter la lecture et l’édition du code source Python. 
- Vous pouvez maintenant lancer votre IDE favori directement à partir d’un fichier, et non plus à partir de l’ensemble du projet.  Si vous ouvrez un fichier dans Workbench et si vous cliquez sur Modifier, votre IDE (pour le moment, VS Code et PyCharm sont pris en charge) se lance à partir du fichier et du projet actifs.  Vous pouvez également cliquer sur la flèche près du bouton Modifier pour modifier le fichier dans l’éditeur de texte de Workbench.  Les fichiers sont en lecture seule jusqu’à ce que vous cliquiez sur Modifier, ce qui permet d’empêcher les changements accidentels.
- La célèbre bibliothèque de traçage `matplotlib` version 2.1.0 est fournie désormais avec l’application Workbench.
- Nous avons mis à niveau le .NET Core vers la version 2.0 pour le moteur de préparation des données. Cela a permis de ne plus avoir à exécuter brew install openssl durant l’installation de l’application sur macOS. Cela ouvre également la voie à des fonctionnalités de préparation des données plus intéressantes dans un avenir proche. 
- Nous avons mis en place une page d’accueil de l’application spécifique à la version. Ainsi, vous obtenez des notes de publication et des invites de mise à jour plus pertinentes en fonction de votre version actuelle de l’application.
- Si votre nom d’utilisateur local contient un espace, l’application peut maintenant être installée correctement. 

### <a name="detailed-updates"></a>Mises à jour détaillées
Vous trouverez ci-dessous une liste des mises à jour détaillées de chaque zone de composant d’Azure Machine Learning dans ce sprint.

#### <a name="installer"></a>Programme d’installation
- Le programme d’installation de l’application nettoie maintenant le répertoire d’installation créé par l’ancienne version de l’application.
- Correction d’un bogue qui faisait bloquer le programme d’installation à 100 % sur macOS High Sierra.
- Il existe désormais un lien direct vers le répertoire du programme d’installation pour permettre à l’utilisateur de consulter les journaux d’installation en cas d’échec de l’installation.
- L’installation fonctionne désormais correctement pour les utilisateurs dont le nom d’utilisateur comporte un espace.

#### <a name="workbench-authentication"></a>Authentification Workbench
- Prise en charge de l’authentification dans le Gestionnaire proxy.
- La journalisation réussit désormais si l’utilisateur se trouve derrière un pare-feu. 
- Si l’utilisateur dispose de comptes d’expérimentation dans plusieurs régions Azure, et si une région n’est pas disponible, l’application ne se bloque plus.
- Quand l’authentification n’est pas effectuée et que la boîte de dialogue d’authentification est toujours visible, l’application n’essaie plus de charger l’espace de travail à partir du cache local.

#### <a name="workbench-app"></a>Application Workbench
- La coloration syntaxique du code Python est activée dans l’éditeur de texte.
- Le bouton Modifier dans l’éditeur de texte vous permet de modifier le fichier dans un IDE (VS Code et PyCharm sont pris en charge) ou dans l’éditeur de texte intégré.
- L’éditeur de texte est en mode lecture seule par défaut. 
- Désormais, l’état visuel du bouton Enregistrer change et apparaît comme étant désactivé après l’enregistrement du fichier actif, il n’est donc plus erroné.
- Workbench enregistre _tous_ les fichiers non enregistrés quand vous lancez une exécution.
- Workbench mémorise le dernier espace de travail utilisé sur la machine locale pour qu’il s’ouvre automatiquement.
- Désormais, une seule instance de Workbench est autorisée à s’exécuter. Auparavant, plusieurs instances pouvaient être lancées, ce qui entraînait des problèmes pour un même projet.
- La commande Ouvrir un projet du menu Fichier a été renommée en Ajouter un dossier existant en tant que projet 
- Le changement d’onglets est maintenant beaucoup plus rapide.
- Les liens d’aide sont ajoutés à la boîte de dialogue Configuration de l’IDE.
- Le formulaire Contactez-nous mémorise désormais l’adresse e-mail que vous avez entrée précédemment.
- La zone de texte de formulaire des sourires et des smileys mécontents a été agrandie, pour que vous puissiez nous envoyer plus de commentaires ! 
- Le texte d’aide du commutateur `--owner` dans `az ml workspace create` est corrigé.
- Nous avons ajouté une boîte de dialogue « À propos de » pour aider l’utilisateur à afficher et copier facilement le numéro de version de l’application.
- L’élément de menu Suggérer une fonctionnalité a été ajouté au menu ? (Aide).
- Le nom du compte d’expérimentation est à présent visible dans la barre de titre de l’application, avant le nom d’application « Azure Machine Learning Workbench ».
- Une page d’accueil de l’application spécifique à la version s’affiche désormais en fonction de la version de l’application détectée.

#### <a name="data-preparation"></a>Préparation des données 
- Pour éviter les problèmes de sécurité potentiels, aucun site web externe ne peut plus être chargé à partir de l’Inspecteur de carte.
- Les inspecteurs d’histogrammes et de valeurs permettent désormais d’afficher un graphe en échelle logarithmique.
- Quand un calcul est en cours, la barre de qualité des données affiche à présent une couleur distincte pour signaler l’état de « calcul ».
- Les métriques de colonnes affichent maintenant des statistiques pour les colonnes de valeurs catégorielles.
- Le dernier caractère du nom de source de données n’est plus tronqué.
- Désormais, le package de préparation des données reste ouvert quand vous changez d’onglet, ce qui entraîne des gains de performances notables.
- Dans la source de données, quand vous passez de la vue de données à la vue de métriques, l’ordre des colonnes ne change plus.
- L’ouverture d’un fichier `.dprep` ou `.dsource` non valide ne provoque plus le blocage de Workbench.
- Le package de préparation des données peut désormais utiliser un chemin relatif pour la sortie dans la transformation _Écriture des données au format CSV_.
- La transformation _Conserver une colonne_ permet désormais à l’utilisateur d’ajouter des colonnes supplémentaires durant la modification.
- Le menu _Remplacer ceci_ lance en fait la boîte de dialogue _Remplacer une valeur_.
- La transformation _Remplacer une valeur_ fonctionne à présent comme prévu au lieu de générer une erreur.
- Le package de préparation des données utilise désormais un chemin absolu quand il référence des fichiers de données en dehors du dossier de projet, ce qui permet d’exécuter le package dans un contexte local avec un chemin absolu vers le fichier de données.
- La stratégie d’échantillonnage de type _Fichier complet_ est désormais prise en charge durant l’utilisation d’un objet blob Azure en tant que source de données.
- Le code Python généré (à partir du package de préparation des données) comporte à présent CR et LF, ce qui le rend convivial dans Windows.
- La liste déroulante _Choisir des métriques_ masque désormais la propriété quand l’utilisateur passe à la vue de données.
- Workbench peut maintenant traiter les fichiers parquet même quand il utilise le runtime Python. Auparavant, seul Spark pouvait être utilisé pour le traitement des fichiers parquet. 
- Le filtrage des valeurs d’une colonne ayant le type de données _date_ ne provoque plus le blocage du moteur de préparation des données.
- La vue de métriques respecte désormais les mises à jour de la stratégie d’échantillonnage.
- Les travaux d’échantillonnage à distance fonctionnent désormais correctement.

#### <a name="job-execution"></a>Exécution des tâches
- L’argument est maintenant inclus dans l’enregistrement d’historique des exécutions.
- Les travaux démarrés dans l’interface CLI s’affichent désormais automatiquement dans le panneau des travaux de l’historique des exécutions.
- Le panneau des travaux affiche désormais les travaux créés par les utilisateurs invités ajoutés au locataire Azure AD.
- Les actions d’annulation et de suppression du panneau des travaux sont plus stables.
- Quand vous cliquez sur le bouton Exécuter, un message d’erreur se déclenche désormais si les fichiers config sont dans un format incorrect.
- L’arrêt de l’application n’interfère plus avec les travaux lancés dans l’interface CLI.
- Les travaux démarrés dans l’interface CLI continuent à utiliser la sortie standard même après une heure d’exécution.
- Des messages d’erreur plus clairs s’affichent en cas d’échec de l’exécution du package de préparation des données avec Python/PySpark.
- Désormais, `az ml experiment clean` nettoie également les images Docker sur la machine virtuelle distante.
- Désormais, `az ml experiment clean` fonctionne correctement pour la cible locale sur macOS.
- Les messages d’erreur liés au ciblage des exécutions Docker locales ou distantes sont plus faciles à lire et à comprendre.
- Un message d’erreur plus clair s’affiche si le nom de nœud principal du cluster HDInsight n’est pas au format approprié quand il est attaché en tant que cible d’exécution.
- Un message d’erreur plus clair s’affiche quand un secret est introuvable dans le service des informations d’identification. 
- La bibliothèque MMLSpark a été mise à niveau pour prendre en charge Apache Spark 2.2.
- MMLSpark inclut désormais la transformation de l’encodage d’objet (encodage Mesh) pour les documents médicaux.
- `matplotlib` version 2.1.0 est désormais fourni prêt à l’emploi avec Workbench.

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- La recherche d’un nom de Notebook fonctionne désormais correctement dans la vue Notebooks.
- Vous pouvez maintenant supprimer un Notebook dans la vue Notebooks.
- Un nouveau `%upload_artifact` magique a été ajouté pour vous permettre de charger les fichiers produits dans l’environnement d’exécution de Notebook vers le magasin de données de l’historique des exécutions.
- Les erreurs de noyau sont maintenant exposées dans l’état des travaux de Notebook pour faciliter le débogage.
- Désormais, le serveur Jupyter se ferme correctement quand l’utilisateur se déconnecte de l’application.

#### <a name="azure-portal"></a>Portail Azure
- Vous pouvez maintenant créer un compte Expérimentation et un compte Gestion des modèles dans deux nouvelles régions Azure : l’Europe de l’Ouest et le Sud-Est asiatique.
- Désormais, le plan DevTest du compte Gestion des modèles est disponible uniquement s’il est le premier à être créé dans l’abonnement. 
- Le lien d’aide du portail Azure a été mis à jour pour pointer vers la page de documentation appropriée.
- Le champ Description a été supprimé de la page des détails de l’image Docker, car il est non applicable.
- Les détails tels que les paramètres relatifs à AppInsights et à la mise à l’échelle automatique, ont été ajoutés à la page des détails du service web.
- La page Gestion des modèles s’affiche désormais, même si les cookies tiers sont désactivés dans le navigateur. 

#### <a name="operationalization"></a>Opérationnalisation
- Tout service web dont le nom contient « score » s’exécute désormais correctement.
- L’utilisateur peut désormais créer un environnement de déploiement avec uniquement l’accès contributeur à un groupe de ressources Azure ou à l’abonnement. L’accès propriétaire à l’intégralité de l’abonnement n’est plus nécessaire.
- L’interface CLI bénéficie à présent de l’autocomplétion par tabulation sur Linux.
- Le service de construction d’images prend désormais en charge la création d’images pour les services/appareils Azure IoT.

#### <a name="sample-projects"></a>Exemples de projets
- Exemple de projet de [_classification d’iris_](./tutorial-classifying-iris-part-1.md) :
    - `iris_pyspark.py` est renommé `iris_spark.py`.
    - `iris_score.py` est renommé `score_iris.py`.
    - `iris.dprep` et `iris.dsource` ont été mis à jour pour refléter les dernières mises à jour du moteur de préparation des données.
    - Le Notebook `iris.ipynb` a été modifié pour pouvoir fonctionner dans le cluster HDInsight.
    - L’historique des exécutions a été activé dans la cellule de Notebook `iris.ipynb`.
- L’étape relative à la gestion de la valeur d’erreur a été corrigée dans l’exemple de projet de [_préparation de données avancée pour le partage de vélos_](./tutorial-bikeshare-dataprep.md).
- Le format de `docker.runconfig` pour l’exemple de projet sur les [_données de recensement des personnes adultes via MMLSpark_](https://github.com/Azure/MachineLearningSamples-mmlspark) a été mis à jour de JSON à YAML.
- Le format de `docker.runconfig` pour l’exemple de projet de [_réglage distribué d’hyperparamètres_](./scenario-distributed-tuning-of-hyperparameters.md) a été mis à jour de JSON à YAML.
- Nouvel exemple de projet de [_classification d’images à l’aide de CNTK_](./scenario-image-classification-using-cntk.md).


## <a name="2017-10-sprint-0"></a>2017-10 (Sprint 0) 
**Numéro de version** : 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Recherchez votre version](known-issues-and-troubleshooting-guide.md))

Bienvenue dans la première mise à jour d’Azure Machine Learning Workbench. Celle-ci fait suite à la préversion publique dévoilée lors de la conférence Microsoft Ignite 2017. La mise à jour regroupe principalement des correctifs visant à améliorer la fiabilité et la stabilité.  Voici quelques-uns des problèmes critiques que nous avons résolus :

### <a name="new-features"></a>Nouvelles fonctionnalités
- macOS High Sierra est désormais pris en charge

### <a name="bug-fixes"></a>Résolution des bogues
#### <a name="workbench-experience"></a>Expérience dans Workbench
- Le fait de glisser-déplacer un fichier dans Workbench entraîne le blocage de Workbench.
- La fenêtre de terminal dans VS Code configuré comme IDE pour Workbench ne reconnaît pas les commandes _az ml_.

#### <a name="workbench-authentication"></a>Authentification Workbench
Nous avons apporté un certain nombre de modifications pour résoudre plusieurs problèmes qui nous ont été signalés en matière de connexion et d’authentification.
- La fenêtre d’authentification s’ouvre à plusieurs reprises, en particulier quand la connexion Internet n’est pas stable.
- Amélioration des problèmes de fiabilité liés à l’expiration des jetons d’authentification.
- Dans certains cas, la fenêtre d’authentification apparaît deux fois.
- La fenêtre principale de Workbench affiche le message « authentification » alors que le processus d’authentification est terminé et que la boîte de dialogue contextuelle est fermée.
- Si aucune connexion Internet n’est établie, la boîte de dialogue d’authentification s’ouvre avec un écran vide.

#### <a name="data-preparation"></a>Préparation des données 
- Quand une valeur spécifique est filtrée, les erreurs et les valeurs manquantes sont également exclues.
- Le fait de changer une stratégie d’échantillonnage supprime les opérations de jointure existantes suivantes.
- Le remplacement d’une transformation Valeur manquante ne tient pas compte de NaN.
- L’inférence de type de date lève une exception quand la valeur null est rencontrée.

#### <a name="job-execution"></a>Exécution des tâches
- Aucun message d’erreur clair ne s’affiche quand l’exécution d’une tâche ne parvient pas à charger un dossier de projet qui dépasse la taille limite.
- Si le script Python de l’utilisateur change le répertoire de travail, les fichiers écrits dans les dossiers de sortie ne sont pas suivis. 
- Si l’abonnement Azure actif est différent de celui auquel le projet actuel appartient, la soumission de la tâche entraîne une erreur 403.
- Quand Docker n’est pas présent, aucun message d’erreur clair n’est retourné si l’utilisateur tente d’utiliser Docker comme cible d’exécution.
- Le fichier .runconfig n’est pas enregistré automatiquement quand l’utilisateur clique sur le bouton _Exécuter_.

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- Le serveur Notebook ne peut pas démarrer si l’utilisateur utilise certains types de connexion.
- Les messages d’erreur du serveur Notebook ne figurent pas dans les journaux accessibles à l’utilisateur.

#### <a name="azure-portal"></a>Portail Azure
- Si le thème sombre du portail Azure est sélectionné, une boîte noire apparaît à la place du panneau Gestion des modèles.

#### <a name="operationalization"></a>Opérationnalisation
- La réutilisation d’un manifeste pour mettre à jour un service web entraîne la génération d’une nouvelle image Docker avec un nom aléatoire.
- Les journaux du service web ne peuvent pas être extraits à partir d’un cluster Kubernetes.
- Un message d’erreur trompeur est imprimé quand un utilisateur se heurte à des problèmes d’autorisation durant une tentative de création d’un compte Gestion des modèles ou Machine Learning Compute.

## <a name="next-steps"></a>Étapes suivantes

Consultez la vue d’ensemble [d’Azure Machine Learning](overview-what-is-azure-ml.md).