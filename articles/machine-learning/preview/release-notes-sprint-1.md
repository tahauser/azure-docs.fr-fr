---
title: Notes de publication d’Azure Machine Learning Workbench pour la version sprint 1 de novembre 2017
description: Ce document décrit en détail les mises à jour pour la version sprint 1 d’Azure Machine Learning
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-1---november-2017"></a>Sprint 1 - novembre 2017 

**Numéro de version : 0.1.1710.31013**

Bienvenue dans la deuxième mise à jour d’Azure Machine Learning Workbench. Nous continuons à améliorer la sécurité, la stabilité et la maintenabilité de l’application Workbench, de l’interface CLI et de la couche des services backend. Merci beaucoup pour les sourires et les smileys mécontents que vous nous envoyez. La plupart des mises à jour ci-dessous sont les résultats directs de vos commentaires. Continuez à en envoyer !

## <a name="notable-new-features"></a>Nouvelles fonctionnalités notables
- Azure ML est maintenant disponible dans deux nouvelles régions Azure : l’**Europe de l’Ouest** et le **Sud-Est asiatique**. Elles rejoignent les autres régions déjà présentes, **États-Unis de l’Est 2**, **Centre-Ouest des États-Unis** et **Est de l’Australie**, ce qui porte à cinq le nombre total de régions déployées.
- Nous avons activé la coloration syntaxique du code Python dans l’application Workbench pour faciliter la lecture et l’édition du code source Python. 
- Vous pouvez maintenant lancer votre IDE favori directement à partir d’un fichier, et non plus à partir de l’ensemble du projet.  Si vous ouvrez un fichier dans Workbench et si vous cliquez sur Modifier, votre IDE (pour le moment, VS Code et PyCharm sont pris en charge) se lance à partir du fichier et du projet actifs.  Vous pouvez également cliquer sur la flèche près du bouton Modifier pour modifier le fichier dans l’éditeur de texte de Workbench.  Les fichiers sont en lecture seule jusqu’à ce que vous cliquiez sur Modifier, ce qui permet d’empêcher les changements accidentels.
- La célèbre bibliothèque de traçage `matplotlib` version 2.1.0 est fournie désormais avec l’application Workbench.
- Nous avons mis à niveau le .NET Core vers la version 2.0 pour le moteur de préparation des données. Cela a permis de ne plus avoir à exécuter brew install openssl durant l’installation de l’application sur macOS. Cela ouvre également la voie à des fonctionnalités de préparation des données plus intéressantes dans un avenir proche. 
- Nous avons mis en place une page d’accueil de l’application spécifique à la version. Ainsi, vous obtenez des notes de publication et des invites de mise à jour plus pertinentes en fonction de votre version actuelle de l’application.
- Si votre nom d’utilisateur local contient un espace, l’application peut maintenant être installée correctement. 

## <a name="detailed-updates"></a>Mises à jour détaillées
Vous trouverez ci-dessous une liste des mises à jour détaillées de chaque zone de composant d’Azure Machine Learning dans ce sprint.

### <a name="installer"></a>Programme d’installation
- Le programme d’installation de l’application nettoie maintenant le répertoire d’installation créé par l’ancienne version de l’application.
- Correction d’un bogue qui faisait bloquer le programme d’installation à 100 % sur macOS High Sierra.
- Il existe désormais un lien direct vers le répertoire du programme d’installation pour permettre à l’utilisateur de consulter les journaux d’installation en cas d’échec de l’installation.
- L’installation fonctionne désormais correctement pour les utilisateurs dont le nom d’utilisateur comporte un espace.

### <a name="workbench-authentication"></a>Authentification Workbench
- Prise en charge de l’authentification dans le Gestionnaire proxy.
- La journalisation réussit désormais si l’utilisateur se trouve derrière un pare-feu. 
- Si l’utilisateur dispose de comptes d’expérimentation dans plusieurs régions Azure, et si une région n’est pas disponible, l’application ne se bloque plus.
- Quand l’authentification n’est pas effectuée et que la boîte de dialogue d’authentification est toujours visible, l’application n’essaie plus de charger l’espace de travail à partir du cache local.

### <a name="workbench-app"></a>Application Workbench
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

### <a name="data-preparation"></a>Préparation des données 
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

### <a name="job-execution"></a>Exécution des tâches
- L’argument est maintenant inclus dans l’enregistrement d’historique des exécutions.
- Les travaux démarrés dans l’interface CLI s’affichent désormais automatiquement dans le panneau des travaux de l’historique des exécutions.
- Le panneau des travaux affiche désormais les travaux créés par les utilisateurs invités ajoutés au locataire AAD.
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

### <a name="jupyter-notebook"></a>Jupyter Notebook
- La recherche d’un nom de Notebook fonctionne désormais correctement dans la vue Notebooks.
- Vous pouvez maintenant supprimer un Notebook dans la vue Notebooks.
- Un nouveau `%upload_artifact` magique a été ajouté pour vous permettre de charger les fichiers produits dans l’environnement d’exécution de Notebook vers le magasin de données de l’historique des exécutions.
- Les erreurs de noyau sont maintenant exposées dans l’état des travaux de Notebook pour faciliter le débogage.
- Désormais, le serveur Jupyter se ferme correctement quand l’utilisateur se déconnecte de l’application.

### <a name="azure-portal"></a>Portail Azure
- Vous pouvez maintenant créer un compte Expérimentation et un compte Gestion des modèles dans deux nouvelles régions Azure : l’Europe de l’Ouest et le Sud-Est asiatique.
- Désormais, le plan DevTest du compte Gestion des modèles est disponible uniquement s’il est le premier à être créé dans l’abonnement. 
- Le lien d’aide du portail Azure a été mis à jour pour pointer vers la page de documentation appropriée.
- Le champ Description a été supprimé de la page des détails de l’image Docker, car il est non applicable.
- Les détails tels que les paramètres relatifs à AppInsights et à la mise à l’échelle automatique, ont été ajoutés à la page des détails du service web.
- La page Gestion des modèles s’affiche désormais, même si les cookies tiers sont désactivés dans le navigateur. 

### <a name="operationalization"></a>Opérationnalisation
- Tout service web dont le nom contient « score » s’exécute désormais correctement.
- L’utilisateur peut désormais créer un environnement de déploiement avec uniquement l’accès contributeur à un groupe de ressources Azure ou à l’abonnement. L’accès propriétaire à l’intégralité de l’abonnement n’est plus nécessaire.
- L’interface CLI bénéficie à présent de l’autocomplétion par tabulation sur Linux.
- Le service de construction d’images prend désormais en charge la création d’images pour les services/appareils Azure IoT.

### <a name="sample-projects"></a>Exemples de projets
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
