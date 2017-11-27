---
title: "Guide de démarrage rapide pour installer les services Azure Machine Learning | Microsoft Docs"
description: "Ce guide de démarrage rapide montre comment créer des ressources Azure Machine Learning et comment installer Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 10/13/2017
ms.openlocfilehash: fd99fec7ead28c02ffe0d8b8165e8a0ab89d525e
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Créer des comptes en préversion Azure Machine Learning et installer Azure Machine Learning Workbench
Azure Machine Learning Services (préversion) est une solution d’analytique avancée et de science des données de bout en bout intégrée. Cette solution permet aux scientifiques des données professionnels de préparer des données, de développer des expérimentations et de déployer des modèles à l’échelle du cloud.

Ce guide de démarrage rapide vous montre comment créer des comptes Expérimentation et Gestion des modèles dans la préversion Azure Machine Learning. Il vous montre également comment installer l’application de bureau Azure Machine Learning Workbench et les outils CLI. Les fonctionnalités de la préversion Azure Machine Learning vous sont ensuite rapidement présentées en utilisant le [jeu de données Fleurs d’iris](https://en.wikipedia.org/wiki/iris_flower_data_set) pour générer un modèle afin de prédire le type d’iris en fonction de certaines de ses caractéristiques physiques.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Composants requis

Actuellement, vous pouvez uniquement installer l’application de bureau Azure Machine Learning Workbench sur les systèmes d’exploitation suivants : 
- Windows 10
- Windows Server 2016
- macOS Sierra
- macOS High Sierra

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-azure-machine-learning-accounts"></a>Créer des comptes Azure Machine Learning
Utilisez le portail Azure pour approvisionner les comptes Azure Machine Learning : 
1. Cliquez sur le bouton **Nouveau** (+) dans le coin supérieur gauche du portail.

2. Tapez **Machine Learning** dans la barre de recherche. Sélectionnez le résultat de la recherche intitulé **Machine Learning Experimentation (preview)** (Expérimentation Machine Learning préversion.  Cliquez sur l’étoile pour ajouter cette sélection aux favoris dans le portail Azure.

   ![Recherche Azure Machine Learning](media/quickstart-installation/portal-more-services.png)

3. Sélectionnez **+ Ajouter** pour configurer un nouveau compte Machine Learning - Expérimentation. Le formulaire détaillé s’ouvre.

   ![Compte Machine Learning - Expérimentation](media/quickstart-installation/portal-create-experimentation.png)

4. Remplissez le formulaire Expérimentation Machine Learning avec les informations suivantes :

   Paramètre|Valeur suggérée|Description
   ---|---|---
   Nom du compte Expérimentation | _Nom unique_ |Choisissez un nom unique qui identifie votre compte. Vous pouvez utiliser votre propre nom, ou celui d’un service ou projet qui identifie le mieux l’expérimentation. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés. 
   Abonnement | _Votre abonnement_ |Choisissez l’abonnement Azure que vous souhaitez utiliser pour votre expérimentation. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource est facturée.
   Groupe de ressources | _Votre groupe de ressources_ | Vous pouvez définir un nouveau nom de groupe de ressources ou utiliser un nom de groupe existant dans votre abonnement.
   Lieu | _La région la plus proche de vos utilisateurs_ | Choisissez l’emplacement le plus proche de vos utilisateurs et des ressources de données.
   Nombre d’utilisateurs | 2 | Entrez le nombre d’utilisateurs. Cette sélection a une incidence sur les [tarifs](https://azure.microsoft.com/pricing/details/machine-learning/). Les deux premiers utilisateurs sont gratuits. Utilisez deux utilisateurs dans le cadre de ce guide de démarrage rapide. Vous pouvez mettre à jour le nombre d’utilisateurs ultérieurement si nécessaire dans le portail Azure.
   Compte de stockage | _Nom unique_ | Sélectionnez **Créer** et indiquez un nom pour créer un compte de stockage Azure. Vous pouvez également sélectionner **Utiliser l’existant** et choisir votre compte de stockage existant dans la liste déroulante. Le compte de stockage est obligatoire et sert à stocker les artefacts de projet et les données de l’historique des exécutions. 
   Espace de travail pour le compte Expérimentation | _Nom unique_ | Entrez un nom pour le nouvel espace de travail. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés.
   Affecter le propriétaire de l’espace de travail | _Votre compte_ | Sélectionnez votre propre compte comme propriétaire de l’espace de travail.
   Créer un compte Gestion des modèles | *check* | Dans le cadre de l’expérience de création du compte Expérimentation, vous avez également la possibilité de créer le compte Gestion des modèles Machine Learning. Cette ressource est utilisée quand vous êtes prêt à déployer et gérer vos modèles en tant que services web en temps réel. Nous vous recommandons de créer le compte Gestion des modèles en même temps que le compte Expérimentation.
   Nom du compte | _Nom unique_ | Choisissez un nom unique qui identifie votre compte Gestion des modèles. Vous pouvez utiliser votre propre nom, ou celui d’un service ou projet qui identifie le mieux l’expérimentation. Le nom doit inclure entre 2 et 32 caractères. Seuls des caractères alphanumériques et des tirets peuvent être utilisés. 
   Niveau tarifaire de Gestion des modèles | **DEVTEST** | Sélectionnez **Aucun niveau tarifaire sélectionné** pour spécifier le niveau tarifaire de votre nouveau compte Gestion des modèles. Pour réduire les coûts, sélectionnez le niveau tarifaire **DEVTEST** s’il est disponible sur votre abonnement (disponibilité limitée). Dans le cas contraire, sélectionnez le niveau tarifaire S1. Cliquez sur **Sélectionner** pour enregistrer la sélection du niveau tarifaire. 
   Épingler au tableau de bord | _check_ | Sélectionnez l’option **Épingler au tableau de bord** pour faciliter le suivi de votre compte Expérimentation Machine Learning sur la première page du tableau de bord du portail Azure.

5. Sélectionnez **Créer** pour commencer le processus de création.

6. Dans la barre d’outils du portail Azure, cliquez sur **Notifications** (icône représentant une cloche) pour surveiller le processus de déploiement. 

   La notification indique **Déploiement en cours**. L’état devient **Déploiement réussi** une fois que l’opération est terminée. La page de votre compte Expérimentation Machine Learning s’ouvre en cas de réussite.
   
   ![Notifications du portail Azure](media/quickstart-installation/portal-notification.png)

À présent, selon le système d’exploitation que vous utilisez sur votre ordinateur local, suivez l’une des deux sections ci-dessous pour installer Azure Machine Learning Workbench. 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Installer Azure Machine Learning Workbench sur Windows
Installez Azure Machine Learning Workbench sur votre ordinateur exécutant Windows 10, Windows Server 2016 ou une version plus récente.

1. Téléchargez le programme d’installation d’Azure Machine Learning Workbench le plus récent [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).

2. Double-cliquez sur le programme d’installation téléchargé **AmlWorkbenchSetup.msi** à partir de l’Explorateur de fichiers.

   >[!IMPORTANT]
   >Téléchargez le programme d’installation entièrement sur le disque, puis exécutez-le à partir de là. Ne l’exécutez pas directement à partir du widget de téléchargement de votre navigateur.

3. Terminez l’installation en suivant les instructions à l’écran.

   Le programme d’installation télécharge tous les composants dépendants nécessaires, tels que Python, Miniconda et d’autres bibliothèques associées. L’installation peut prendre environ une demi-heure pour l’ensemble des composants. 

4. Azure Machine Learning Workbench est maintenant installé dans le répertoire suivant :
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>Installer Azure Machine Learning Workbench sur macOS
Installez Azure Machine Learning Workbench sur votre ordinateur exécutant macOS Sierra ou une version ultérieure.

1. Téléchargez le programme d’installation d’Azure Machine Learning Workbench le plus récent, [AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg).

   >[!IMPORTANT]
   >Téléchargez le programme d’installation entièrement sur le disque, puis exécutez-le à partir de là. Ne l’exécutez pas directement à partir du widget de téléchargement de votre navigateur.

2. Double-cliquez sur le programme d’installation téléchargé **AmlWorkbench.dmg** à partir de Finder.

3. Terminez l’installation en suivant les instructions à l’écran.

   Le programme d’installation télécharge tous les composants dépendants nécessaires, tels que Python, Miniconda et d’autres bibliothèques associées. L’installation peut prendre environ une demi-heure pour l’ensemble des composants. 

4. Azure Machine Learning Workbench est maintenant installé dans le répertoire suivant : 

   `/Applications/Azure ML Workbench.app`

## <a name="run-azure-machine-learning-workbench-to-sign-in-for-the-first-time"></a>Exécuter Azure Machine Learning Workbench pour la première connexion
1. Une fois le processus d’installation terminé, cliquez sur le bouton **Launch Workbench** (Lancer Workbench) sur le dernier écran du programme d’installation. Si vous avez fermé le programme d’installation, recherchez le raccourci vers Machine Learning Workbench sur votre bureau et le menu de **démarrage** nommé **Azure Machine Learning Workbench** pour lancer l’application.

2. Connectez-vous à Workbench avec le même compte que celui que vous avez utilisé précédemment pour approvisionner vos ressources Azure. 

3. Une fois le processus de connexion terminé, Workbench tente de trouver les comptes Machine Learning - Expérimentation que vous avez créés précédemment. Il recherche tous les abonnements Azure auxquels vos informations d’identification ont accès. Quand au moins un compte Expérimentation est trouvé, Workbench s’ouvre avec ce compte. Il répertorie ensuite les espaces de travail et projets se trouvant dans ce compte. 

   >[!TIP]
   > Si vous avez accès à plusieurs comptes Expérimentation, vous pouvez basculer vers un autre compte en sélectionnant l’icône représentant un avatar dans le coin inférieur gauche de l’application Workbench.

Consultez [Installation de la gestion des modèles](deployment-setup-configuration.md) pour plus d’informations sur la création d’un environnement pour le déploiement de vos services web.

## <a name="create-a-new-project"></a>Création d'un projet
1. Lancez l’application Azure Machine Learning Workbench et connectez-vous. 

2. Sélectionnez **Fichier** > **Nouveau projet** (ou sélectionnez le signe **+** dans le volet **PROJETS**). 

3. Renseignez les champs **Nom du projet** et **Répertoire du projet**. Le champ **Description du projet** est facultatif, mais utile. Laissez le champ **URL du dépôt GIT Visualstudio.com** vide pour l’instant. Choisissez un espace de travail, puis sélectionnez **Classifying Iris** (Classification d’iris) comme modèle de projet.

   >[!TIP]
   >Si vous le souhaitez, vous pouvez renseigner la zone de texte du dépôt Git avec l’URL d’un dépôt Git qui est hébergé dans un projet [Visual Studio Team Services](https://www.visualstudio.com). Ce dépôt Git doit déjà exister et il doit être vide, sans aucune branche maître. Vous devez disposer d’un accès en écriture à ce référentiel. L’ajout d’un dépôt Git maintenant vous permet d’activer des scénarios d’itinérance et de partage plus tard. [En savoir plus](using-git-ml-project.md).

4. Sélectionnez le bouton **Créer** pour créer le projet. Un projet est créé et ouvert pour vous. À ce stade, vous pouvez explorer la page d’accueil du projet, les sources de données, les blocs-notes et les fichiers de code source. 

    >[!TIP]
    >Vous pouvez également ouvrir le projet dans Visual Studio Code ou d’autres éditeurs simplement en configurant un lien de l’IDE (environnement de développement intégré), puis en y ouvrant le répertoire du projet. [En savoir plus](how-to-configure-your-IDE.md). 

## <a name="run-a-python-script"></a>Exécuter un script Python
Nous allons exécuter un script sur votre ordinateur local. 

1. Chaque projet s’ouvre dans sa propre page **Tableau de bord Projet**. Sélectionnez **locale** comme cible d’exécution à partir de la barre de commandes près du haut de l’application, puis sélectionnez **iris_sklearn.py** comme script à exécuter. D’autres fichiers inclus dans l’exemple pourront être extraits plus tard. 

   ![Barre de commandes](media/quickstart-installation/run_control.png)

2. Dans la zone de texte **Arguments**, entrez **0,01**. Ce nombre est utilisé dans le code pour définir le taux de régularisation. Il s’agit d’une valeur utilisée pour configurer la façon dont le modèle de régression linéaire est formé. 

3. Sélectionnez le bouton **Exécuter** pour commencer à exécuter **iris_sklearn.py** sur votre ordinateur. 

   Ce code utilise l’algorithme de [régression logistique](https://en.wikipedia.org/wiki/logistic_regression) de la populaire bibliothèque [scikit-learn](http://scikit-learn.org/stable/index.html) Python pour générer le modèle.

4. Le panneau **Tâches** sort de la droite s’il n’est pas déjà visible et un travail **iris_sklearn** est ajouté dans le panneau. Son état passe d’**Envoi** à **Exécution** quand l’exécution de la tâche commence, puis à **Terminé** en quelques secondes. 

   Félicitations ! Vous avez exécuté un script Python dans Azure Machine Learning Workbench.

6. Répétez les étapes 2 à 4 plusieurs fois. À chaque fois, utilisez des valeurs d’arguments différentes allant de **10** à **0,001**.

## <a name="view-run-history"></a>Afficher l’historique des exécutions
1. Accédez à l’affichage **Exécutions** et sélectionnez **iris_sklearn.py** dans la liste des exécutions. Le tableau de bord de l’historique des exécutions pour **iris_sklearn.py** s’ouvre. Il montre chaque exécution effectuée sur **iris_sklearn.py**. 

   ![Tableau de bord de l’historique des exécutions](media/quickstart-installation/run_view.png)

2. Le tableau de bord de l’historique des exécutions affiche également les principales métriques, un ensemble de graphes par défaut et une liste de métriques pour chaque exécution. Vous pouvez personnaliser cet affichage en triant, filtrant et ajustant les configurations. Il vous suffit de sélectionner l’icône de configuration ou l’icône de filtre.

   ![Métriques et graphiques](media/quickstart-installation/run_dashboard.png)

3. Sélectionnez une exécution terminée pour obtenir des informations sur celle-ci. Vous obtiendrez notamment des métriques supplémentaires, les fichiers générés suite à l’exécution et d’autres journaux pouvant être utiles.

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant créé un compte Expérimentation Azure Machine Learning et un compte Gestion des modèles Azure Machine Learning. Vous avez installé l’application de bureau Azure Machine Learning Workbench et une interface de ligne de commande. Vous avez créé un projet, créé un modèle en exécutant un script et exploré l’historique des exécutions du script.

Pour obtenir une expérience plus approfondie de ce flux de travail, notamment la façon de déployer votre modèle Iris en tant que service web, suivez le didacticiel *Classifying Iris* (Classification d’iris) intégral. Ce dernier contient des étapes détaillées pour la [préparation des données](tutorial-classifying-iris-part-1.md), [l’expérimentation](tutorial-classifying-iris-part-2.md) et la [gestion des modèles](tutorial-classifying-iris-part-3.md). 

> [!div class="nextstepaction"]
> [Didacticiel Classifying Iris](tutorial-classifying-iris-part-1.md) (Classification d’iris)
