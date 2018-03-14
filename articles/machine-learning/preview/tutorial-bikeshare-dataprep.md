---
title: "Didacticiel BikeShare - Préparation de données avancée avec Azure Machine Learning Workbench"
description: "Dans ce didacticiel, vous allez réaliser une tâche de préparation de données de bout en bout avec Azure Machine Learning Workbench"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: ca7239fd3e31c7a6cfc6fb64e04afb376e01c190
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>Didacticiel : Préparation de données avancée avec Azure Machine Learning Workbench (données BikeShare)
Azure Machine Learning (préversion) constitue une solution d’analytique avancée et de science des données de bout en bout intégrée, destinée aux chercheurs de données professionnels pour préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Dans ce didacticiel, vous utilisez les services Machine Learning (préversion) pour apprendre à effectuer les tâches suivantes :
> [!div class="checklist"]
> * Préparer des données de manière interactive avec l’outil de préparation des données Machine Learning.
> * Importer, transformer et créer un jeu de données de test.
> * Générer un package de préparation des données.
> * Exécuter le package de préparation des données à l’aide de Python.
> * Générer un jeu de données d’apprentissage en réutilisant le package de préparation des données pour des fichiers d’entrée supplémentaires.
> * Exécuter des scripts dans une fenêtre Azure CLI locale.
> * Exécuter des scripts dans un environnement Azure HDInsight cloud.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Installation locale d’Azure Machine Learning Workbench. Pour plus d’informations, suivez le [Guide de démarrage rapide sur l’installation](quickstart-installation.md).
* Si Azure CLI n’est pas installé, suivez les instructions pour [installer la dernière version d’Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
* Un [cluster HDInsight Spark](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal) créé dans Azure.
* Un compte de stockage Azure.
* Vous devez savoir créer un projet dans Workbench.
* Bien que ce ne soit pas obligatoire, il est utile d’installer [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour pouvoir charger, télécharger et afficher les objets blob dans le compte de stockage.

## <a name="data-acquisition"></a>Acquisition de données
Ce didacticiel utilise le [jeu de données Boston Hubway](https://s3.amazonaws.com/hubway-data/index.html) et les données météorologiques de Boston diffusées par l’agence [NOAA](http://www.noaa.gov/).

1. Téléchargez les fichiers de données à partir des liens suivants dans votre environnement de développement local :

   * [Données météorologiques de Boston](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Données de trajet Hubway issues du site web Hubway :

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Décompressez chaque fichier .zip après son téléchargement.

## <a name="upload-data-files-to-azure-blob-storage"></a>Charger des données dans le Stockage Blob Azure
Vous pouvez utiliser le Stockage Blob Azure pour héberger vos fichiers de données.

1. Utilisez le compte de stockage associé au cluster HDInsight que vous utilisez.

    ![Compte de stockage du cluster HDInsight](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

2. Créez un conteneur nommé **data-files** pour stocker les fichiers de données **BikeShare**.

3. Chargez les fichiers de données. Chargez `BostonWeather.csv` dans un dossier intitulé `weather`. Chargez les fichiers de données de trajet dans un dossier nommé `tripdata`.

    ![Charger des fichiers de données](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> Vous pouvez également utiliser l’Explorateur Stockage pour charger des objets blob. Utilisez cet outil pour afficher le contenu de chacun des fichiers générés dans le didacticiel.

## <a name="learn-about-the-datasets"></a>En savoir plus sur les jeux de données
1. Le fichier __Boston weather__ contient les champs d’informations météo suivants, consignées toutes les heures :

   * **DATE**

   * **REPORTTPYE**

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Les données __Hubway__ sont organisées dans des fichiers par année et mois. Par exemple, le fichier nommé `201501-hubway-tripdata.zip` contient un fichier .csv contenant les données de janvier 2015. Les données contiennent les champs suivants, où chaque ligne représente un trajet à vélo :

   * **Durée du trajet (en secondes)**

   * **Date et heure de départ**

   * **Date et heure d’arrivée**

   * **Nom et ID de la station de départ**

   * **Nom et ID de la station d’arrivée**

   * **ID de vélo**

   * **Type d’utilisateur (Occasionnel = utilisateur avec carte 24 ou 72 heures ; Membre = adhésion annuelle ou mensuelle)**

   * **Code postal \(si l’utilisateur est membre)**

   * **Sexe \(indiqué par le membre)**

## <a name="create-a-new-project"></a>Création d'un projet
1. Lancez **Machine Learning Workbench** à partir de votre menu Démarrer ou du lanceur.

2. Créez un projet Machine Learning. Cliquez sur le bouton **+** dans la page **Projets**, ou bien sur **Fichier** > **Nouveau**.

   * Utilisez le modèle **Bike Share**.

   * Nommez votre projet **BikeShare**. 

## <a id="newdatasource"></a>Créer une source de données

1. Créez une source de données. Cliquez sur le bouton **Données** (icône représentant un cylindre) dans la barre d’outils à gauche. pour afficher l’onglet Vue de **Données**.

   ![Onglet Vue de données](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Ajoutez une source de données. Sélectionnez l’icône **+**, puis sélectionnez **Ajouter une source de données**.

   ![Option Ajouter une source de données](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Ajouter les données météorologiques

1. **Magasin de données** : Sélectionnez le magasin de données qui contient les données. Étant donné que vous utilisez des fichiers, sélectionnez **Fichier(s)/Répertoire**. Sélectionnez **Suivant** pour continuer.

   ![Entrée Fichier(s)/Répertoire](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Sélection de fichiers** : Ajoutez les données météorologiques. Recherchez et sélectionnez le fichier `BostonWeather.csv` que vous avez chargé tout à l’heure dans le Stockage Blob. Sélectionnez **Suivant**.

   ![Sélection de fichiers avec BostonWeather.csv sélectionné](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

3. **Détails du fichier** : Vérifiez le schéma de fichier détecté. Machine Learning Workbench analyse les données dans le fichier et en déduit le schéma à utiliser.

   ![Vérification des détails du fichier](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > Workbench ne détecte pas le bon schéma dans certains cas. Vérifiez toujours que les paramètres sont corrects pour votre jeu de données. Vérifiez que les données météorologiques ont les valeurs suivantes :
   >
   > * __Type de fichier__ : Fichier délimité (csv, tsv, txt, etc.)
   > * __Séparateur__ : Virgule [,]
   > * __Caractère de la ligne de commentaire__ : Aucune valeur définie
   > * __Ignorer le mode de lignes__ : Ne pas ignorer
   > * __Encodage du fichier__ : utf-8
   > * __Promouvoir le mode en-têtes__ : Utiliser les en-têtes à partir du premier fichier

   L’aperçu des données doit présenter les colonnes suivantes :

   * **Chemin d’accès**

   * **DATE**

   * **REPORTTYPE**

   * **HOURLYDRYBULBTEMPF**
   
   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

   Pour continuer, sélectionnez **Suivant**.

4. **Types de données** : Passez en revue les types de données qui sont détectés automatiquement. Machine Learning Workbench analyse les données dans le fichier et en déduit les types de données à utiliser.

   a. Pour ces données, modifiez **TYPE DE DONNÉES** dans toutes les colonnes de **Chaîne**.

   > [!NOTE]
   > Chaîne est utilisé pour mettre en évidence les fonctionnalités de Workbench plus loin dans ce didacticiel. 

   ![Analyse des données système](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   b. Pour continuer, sélectionnez __Suivant__. 

5. **Échantillonnage** : Pour créer un schéma d’échantillonnage, sélectionnez **Modifier**. Sélectionnez la nouvelle ligne __Top 10000__ qui s’ajoute, puis sélectionnez __Modifier__. Définissez l’__exemple de stratégie__ sur **Fichier complet**, puis sélectionnez **Appliquer**.

   ![Ajout d’une nouvelle stratégie d’échantillonnage](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   Pour utiliser la stratégie __Fichier complet__, sélectionnez l’entrée __Fichier complet__, puis sélectionnez __Définir comme actif__. Une étoile apparaît en regard de __Fichier complet__ pour indiquer qu’il s’agit de la stratégie active.

   ![Fichier complet défini comme stratégie active](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Pour continuer, sélectionnez **Suivant**.

6. **Colonne de chemin** : utilisez la section __Colonne de chemin__ pour inclure le chemin de fichier complet en tant que colonne dans les données importées. Sélectionnez __Ne pas inclure la colonne de chemin__.

   > [!TIP]
   > Inclure le chemin en tant que colonne s’avère utile si vous importez un dossier comprenant de nombreux fichiers portant des noms différents. Cela s’avère également utile si les noms de fichier contiennent des informations que vous voulez extraire plus tard.

   ![Colonne de chemin défini sur Ne pas inclure](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Terminer** : Pour terminer la création de la source de données, sélectionnez **Terminer**.

    Un nouvel onglet de source de données nommé __BostonWeather__ s’ouvre. Un exemple des données s’affiche dans une grille. L’exemple s’appuie sur le schéma d’échantillonnage actif spécifié précédemment.

    Remarque : le volet **Étapes** du côté droit de l’écran affiche les actions individuelles effectuées lors de la création de cette source de données.

   ![Affichage de la source de données, l’exemple et les étapes](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Afficher les métriques de la source de données

Sélectionnez __Métriques__ en haut à gauche de la grille de l’onglet. Cette vue affiche la distribution et d’autres statistiques agrégées des données échantillonnées.

![Affichage des métriques](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Pour configurer la visibilité des statistiques, utilisez la liste déroulante **Choisir une métrique**. Sélectionnez et videz les métriques pour modifier la grille.

Pour retourner dans la vue de __données__, sélectionnez __Données__ en haut à gauche de la page.

## <a name="add-a-data-source-to-the-data-preparation-package"></a>Ajout de la source de données au package de préparation des données

1. Sélectionnez __Préparer__ pour commencer la préparation des données. 

2. Lorsque vous y êtes invité, entrez un nom pour le package de préparation des données, comme **BikeShare Data Prep**. 

3. Sélectionnez __OK__ pour continuer.

   ![Boîte de dialogue Préparer](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Un nouveau package nommé **BikeShare Data Prep** apparaît sous la section __Préparation des données__ de l’onglet __Données__. 

   Pour afficher le package, sélectionnez cette entrée. 

5. Sélectionnez le bouton **>>** pour développer l’affichage des __flux de données__ contenus dans le package. Dans cet exemple, __BostonWeather__ est le seul flux de données.

   > [!IMPORTANT]
   > Un package peut contenir plusieurs flux de données.

   ![Flux de données dans le package](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrer les données par valeur
1. Pour filtrer les données, cliquez avec le bouton droit sur une cellule contenant une certaine valeur, puis sélectionnez __Filtrer__. Sélectionnez ensuite le type de filtre.

2. Pour ce didacticiel, sélectionnez une cellule qui contient la valeur `FM-15`. Puis définissez le filtre sur **Égale**.  À présent, les données sont filtrées pour ne renvoyer que les lignes où la valeur de __REPORTTYPE__ est `FM-15`.

   ![Boîte de dialogue Filtre](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 est un type de rapport d’observation météorologique pour l’aviation (METAR). Les rapports FM 15 sont observés de manière empirique afin d’être les plus complets possible, avec peu de données manquantes.

## <a name="remove-a-column"></a>Supprimer une colonne

Vous n’avez plus besoin de la colonne __REPORTTYPE__. Cliquez avec le bouton droit sur l’en-tête de colonne et sélectionnez **Supprimer une colonne**.

   ![Option Supprimer une colonne](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Modifier les types de données et supprimer des erreurs
1. Appuyer sur Ctrl (Command ⌘ sur Mac) pendant la sélection d’en-têtes de colonne pour sélectionner plusieurs colonnes à la fois. Utilisez cette technique pour sélectionner les en-têtes de colonne suivants :

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Cliquez avec le bouton droit sur l’un des en-têtes de colonne sélectionnés, puis sélectionnez **Convertir le champ en type numérique**. Cette opération convertit le type de données de ces colonnes en type numérique.

   ![Conversion de plusieurs colonnes en type numérique](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtrez les valeurs d’erreur. Certaines colonnes ont des problèmes de conversion des types de données. Ce problème est indiqué par la couleur rouge dans la __barre de qualité des données__ de la colonne.

   Pour supprimer les lignes qui contiennent des erreurs, cliquez avec le bouton droit sur l’en-tête de colonne **HOURLYDRYBULBTEMPF**. Sélectionnez **Filtrer la colonne**. Pour l’option **Je veux**, utilisez la valeur par défaut **Conserver les lignes**. Modifiez la valeur indiquée dans la liste déroulante **Conditions** pour sélectionner **n’est pas une erreur**. Sélectionnez **OK** pour appliquer le filtre.

   ![Filtrer des valeurs d’erreur](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

4. Pour éliminer les lignes d’erreur restantes dans les autres colonnes, répétez ce processus de filtrage pour les colonnes **HOURLYRelativeHumidity** et **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Utiliser des transformations par un exemple

Pour utiliser les données dans une prévision par bloc de deux heures, vous devez calculer les conditions météo moyennes pendant des périodes de deux heures. Effectuez les actions suivantes :

* Fractionnez la colonne **DATE** dans des colonnes **Date** et **Time** distinctes. Consultez la section suivante pour connaître les étapes détaillées.

* Dérivez une colonne **Hour_Range** à partir de la colonne **Time**. Consultez la section suivante pour connaître les étapes détaillées.

* Dérivez une colonne **Date\_Hour\_Range** à partir des colonnes **DATE** et **Hour_Range**. Consultez la section suivante pour connaître les étapes détaillées.

### <a name="split-column-by-example"></a>Fractionner la colonne par un exemple

1. Fractionnez la colonne **DATE** dans des colonnes **Date** et **Time** distinctes. Cliquez avec le bouton droit sur l’en-tête de colonne **DATE** et sélectionnez **Fractionner la colonne par un exemple**.

   ![Entrée Fractionner des colonnes par l’exemple](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Machine Learning Workbench identifie automatiquement un délimiteur significatif et crée deux colonnes en fractionnant les données dans des valeurs de date et d’heure. 

3. Sélectionnez __OK__ pour accepter les résultats de l’opération de fractionnement.

   ![Colonnes fractionnées DATE_1 et DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Dériver une colonne par un exemple

1. Pour dériver une plage de deux heures, cliquez avec le bouton droit sur l’en-tête de colonne __DATE\_2__ et sélectionnez **Dériver une colonne par un exemple**.

   ![Entrée Dériver des colonnes par l’exemple](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Une nouvelle colonne vide est ajoutée avec des valeurs Null.

2. Cliquez dans la première cellule vide de la nouvelle colonne. Pour donner un exemple de la plage de temps souhaitée, tapez **12AM-2AM** dans la nouvelle colonne, puis appuyez sur Entrée.

   ![Nouvelle colonne présentant une valeur 12AM-2AM](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Machine Learning Workbench synthétise un programme basé sur les exemples que vous fournissez et applique le même programme sur les lignes restantes. Toutes les autres lignes sont automatiquement renseignées en fonction de l’exemple que vous avez fourni. Workbench analyse également vos données et tente d’identifier les cas marginaux. 

   > [!IMPORTANT]
   > Identification des cas marginaux peut ne pas fonctionner sur Mac dans la version actuelle de Workbench. Sur Mac, ignorez les étapes 3 et 4. Au lieu de cela, appuyez sur __OK__ une fois toutes les lignes remplies avec les valeurs dérivées.
   
3. Le texte **Analyse des données** au-dessus de la grille indique que Workbench tente de détecter les cas marginaux. Une fois l’opération terminée, l’état passe à **Passer en revue la ligne suggérée suivante** ou **Aucune suggestion**. Dans cet exemple, **Passer en revue la ligne suggérée suivante** est retourné.

4. Pour passer en revue les modifications suggérées, sélectionnez **Passer en revue la ligne suggérée suivante**. La cellule que vous devez passer en revue et corriger (si besoin) est mise en surbrillance dans l’affichage.

   ![Passer en revue la ligne suggérée suivante](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Sélectionnez __OK__ pour accepter la transformation.
 
5. Vous revenez à la grille des données de __BostonWeather__. Désormais, la grille contient les trois colonnes ajoutées précédemment.

   ![Grille avec lignes ajoutées](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > Toutes les modifications apportées sont conservées dans le volet **Étapes**. Accédez à l’étape que vous avez créée dans le volet **Étapes**, sélectionnez la flèche vers le bas, puis **Modifier**. La fenêtre avancée **Dériver une colonne par un exemple** s’affiche. Tous vos exemples y sont conservés. Vous pouvez également ajouter manuellement des exemples en double-cliquant sur une ligne dans la grille suivante. Sélectionnez **Annuler** pour revenir à la grille principale sans appliquer les modifications. Vous pouvez également accéder à cette vue en sélectionnant **Mode avancé** quand vous effectuez une transformation **Dériver une colonne par un exemple**.

6. Pour renommer la colonne, double-cliquez sur son en-tête et tapez **Plage horaire**. Appuyez sur Entrée pour enregistrer la modification.

   ![Renommer les colonnes](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Pour dériver la plage de dates et heures, sélectionnez les colonnes **Date\_1** et **Plage horaire**, cliquez avec le bouton droit et sélectionnez **Dériver une colonne par un exemple**.

   ![Dériver des colonnes par l’exemple](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Tapez **Jan 01, 2015 12AM-2AM** comme exemple contre la première ligne, et sélectionnez Entrée.

   Workbench détermine la transformation en fonction de l’exemple que vous fournissez. Dans cet exemple, le résultat est que le format de la date est modifié et concaténé avec la fenêtre de deux heures.

   ![Exemple Jan 01, 2015 12AM-2AM](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > Sur Mac, effectuez l’étape suivante au lieu de l’étape 8 :
   >
   > * Accédez à la première cellule qui contient **Feb 01, 2015 12AM-2AM**. Il doit s’agir de la ligne 15. Corrigez la valeur en **Jan 02, 2015 12AM-2AM** puis sélectionnez Entrée. 
   

8. Attendez que l’état passe de **Analyse des données** à **Passer en revue la ligne suggérée suivante**. Cette modification peut prendre plusieurs secondes. Sélectionnez le lien d’état pour accéder à la ligne suggérée. 

   ![Ligne suggérée à passer en revue](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   La ligne a une valeur Null, car la valeur de date source peut être soit jj/mm/aaaa, soit mm/jj/aaaa. Tapez la bonne valeur **Jan 13, 2015 2AM-4AM**, puis sélectionnez Entrée. Workbench utilise les deux exemples pour améliorer la dérivation des lignes restantes.

   ![Données correctement formatées](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Sélectionnez **OK** pour accepter la transformation.

   ![Grille de la transformation terminée](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Pour utiliser le **Mode avancé** de **Dériver une colonne par un exemple** dans cette étape, sélectionnez la flèche bas dans le volet **Étapes**. Dans la grille de données, des cases à cocher se trouvent en regard des noms de colonne **DATE\_1** et **Plage horaire**. Décochez celle située à côté de la colonne **Plage horaire** pour voir comment cela modifie la sortie. En l’absence de colonne **Plage horaire**en tant qu’entrée, la valeur **12AM-2AM** est traitée comme une constante et ajoutée aux valeurs dérivées. Sélectionnez **Annuler** pour revenir à la grille principale sans appliquer les modifications.
   ![Mode avancé](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

10. Pour renommer la colonne, double-cliquez sur son en-tête. Remplacez le nom par **Date Plage horaire**, puis sélectionnez Entrée.

11. Sélectionnez simultanément les colonnes **DATE**, **DATE\_1**, **DATE\_2** et **Plage horaire**. Cliquez avec le bouton droit et sélectionnez **Supprimer la colonne**.

## <a name="summarize-data-mean"></a>Résumer les données (moyenne)

L’étape suivante consiste à résumer les conditions météo en prenant la moyenne des valeurs, regroupées par plage horaire.

1. Sélectionnez la colonne **Date Plage horaire**, puis sélectionnez **Résumer** dans le menu **Transformations**.

    ![Menu Transformations](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Pour résumer les données, vous faites glisser des colonnes de la grille située au bas de la page vers les volets situés en haut à gauche et à droite. Le volet gauche contient le texte **Faites glisser les colonnes ici pour regrouper les données**. Le volet droit contient le texte **Faites glisser les colonnes ici pour résumer les données**. 

    a. Faites glisser la colonne **Date Plage horaire** de la grille située en bas vers le volet gauche. Faites glisser **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** et **HOURLYWindSpeed** vers le volet droit. 

    b. Dans le volet droit, sélectionnez **Moyenne** comme mesure **Agrégat** pour chaque colonne. Sélectionnez **OK** pour terminer le résumé.

    ![Écran des données résumées](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>Transformer un flux de données à l’aide d’un script

Remplacer les données situées dans les colonnes numériques par une plage de 0 à 1 permet à certains modèles de converger rapidement. Actuellement, aucune transformation intégrée n’est en mesure d’effectuer cette transformation de façon générique. Utilisez un script Python pour effectuer cette opération.

1. Dans le menu **Transformation**, sélectionnez **Transformer le flux de données (script)**.

2. Entrez le code suivant dans la zone de texte qui s’affiche. Si vous avez utilisé les noms de colonne, le code doit fonctionner sans modification. Vous écrivez une logique de normalisation min-max simple dans Python.

    > [!WARNING]
    > Le script attend les noms de colonne utilisés précédemment dans ce didacticiel. Si vous avez des noms de colonne différents, vous devez modifier les noms dans le script.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Le script Python doit retourner `df` à la fin. Cette valeur est utilisée pour renseigner la grille.
    
   ![Boîte de dialogue Transformer le flux de données (script)](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Sélectionnez __OK__ pour utiliser le script. Les colonnes numériques dans la grille contiennent désormais des valeurs comprises dans la plage 0 à 1.

    ![Grille qui contient des valeurs comprises entre 0 et 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Vous avez terminé la préparation des données météorologiques. Ensuite, préparez les données de trajet.

## <a name="load-trip-data"></a>Charger les données de trajet

1. Pour importer le fichier `201701-hubway-tripdata.csv`, utilisez les étapes indiquées dans la section [Créer une source de données](#newdatasource). Pendant le processus d’importation, utilisez les options suivantes :

    * __Sélection du fichier__ : Sélectionnez **Blob Azure** pour rechercher et sélectionner le fichier.

    * __Schéma d’échantillonnage__ : Sélectionnez le schéma d’échantillonnage **Fichier complet** puis activez-le.

    * __Type de données__ : Acceptez les valeurs par défaut.

2. Après avoir importé les données, sélectionnez __Préparer__ pour commencer la préparation des données. Sélectionnez le package **BikeShare Data Prep.dprep** existant, puis sélectionnez __OK__.

    Ce processus ajoute un **flux de données** au fichier de **préparation des données** existant plutôt que d’en créer un nouveau.

    ![Sélectionnez le package existant.](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Une fois la grille chargée, développez __FLUX DE DONNÉES__. Il existe désormais deux flux de données : **BostonWeather** et **201701-hubway-tripdata**. Sélectionnez l’entrée **201701-hubway-tripdata**.

    ![Entrée 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>Utiliser l’inspecteur de carte

Pour la préparation des données, des visualisations utiles, nommées inspecteurs, sont disponibles pour les données de chaînes, numériques et géographiques. Ils permettent de mieux comprendre les données et d’identifier les valeurs hors normes. Procédez comme suit pour utiliser l’inspecteur de carte.

1. Sélectionnez plusieurs colonnes **start station latitude** et **start station longitude**. Cliquez avec le bouton droit sur l’une des colonnes, puis sélectionnez **Carte**.

    > [!TIP]
    > Pour activer la sélection multiple, maintenez la touche Ctrl (Command ⌘ sur Mac) enfoncée et sélectionnez l’en-tête de chaque colonne.

    ![Visualisation de carte](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Pour agrandir la carte, sélectionnez l’icône **Agrandir**. Pour ajuster la carte à la fenêtre, sélectionnez l’icône **E** située dans l’angle supérieur gauche de la visualisation.

    ![Image agrandie](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Sélectionnez le bouton **Réduire** pour revenir à la grille.

## <a name="use-the-column-statistics-inspector"></a>Utiliser l’inspecteur de statistiques de colonne

Pour utiliser l’inspecteur de statistiques de colonne, cliquez avec le bouton droit sur la colonne __tripduration__ et sélectionnez __Statistiques de colonne__.

![Entrée Statistiques de colonne](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Ce processus ajoute une nouvelle visualisation intitulée __Statistiques tripduration__ dans le volet __INSPECTEURS__.

![Inspecteur de statistiques tripduration](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> La valeur maximale de la durée du trajet est 961 814 minutes, ce qui correspond à environ deux ans. Apparemment, il existe des valeurs hors norme dans le jeu de données.

## <a name="use-the-histogram-inspector"></a>Utiliser l’inspecteur d’histogramme

Pour essayer d’identifier les valeurs hors norme, cliquez avec le bouton droit sur la colonne __tripduration__ et sélectionnez __Histogramme__.

![Inspecteur d’histogramme](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

L’histogramme n’est pas utile car les valeurs hors norme inclinent le graphique.

## <a name="add-a-column-by-using-script"></a>Ajouter une colonne à l’aide d’un script

1. Cliquez avec le bouton droit sur la colonne **tripduration** et sélectionnez **Ajouter une colonne (script)**.

    ![Menu Ajouter une colonne (script)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. Dans la boîte de dialogue __Ajouter une colonne (script)__, utilisez les valeurs suivantes :

    * __Nouveau nom de colonne__ : logtripduration

    * __Insérer cette nouvelle colonne après__ : tripduration

    * __Code de la nouvelle colonne__ : `math.log(row.tripduration)`

    * __Type de bloc de code__ : Expression

   ![Boîte de dialogue Ajouter une colonne (script)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Sélectionnez __OK__ pour ajouter la colonne **logtripduration**.

4. Cliquez avec le bouton droit sur la colonne et sélectionnez **Histogramme**.

    ![Histogramme de la colonne logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    Visuellement, cet histogramme semble être une distribution normale avec une fin anormale.

## <a name="use-an-advanced-filter"></a>Utiliser un filtre avancé

L’utilisation d’un filtre sur les données permet de mettre à jour les inspecteurs avec la nouvelle distribution. 

1. Cliquez avec le bouton droit sur la colonne **logtripduration** et sélectionnez **Filtrer la colonne**. 

2. Dans la boîte de dialogue __Modifier__, utilisez les valeurs suivantes :

    * __Filtrer cette colonne numérique__ : logtripduration

    * __Je veux__ : Conserver les lignes

    * __Quand__ : Une ou plusieurs des conditions ci-dessous ont la valeur True (OU logique)

    * __Si cette colonne__ : est inférieure à

    * __La valeur__ : 9

    ![Options de filtre](media/tutorial-bikeshare-dataprep/loftripfilter.png)

3. Sélectionnez __OK__ pour appliquer le filtre.

    ![Histogrammes mis à jour après application du filtre](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Effet de halo

1. Agrandissez l’histogramme **logtripduration**. Un histogramme bleu se superpose à un histogramme gris. Cet affichage est appelé **effet de halo** :

    * L’histogramme gris représente la distribution avant l’opération (dans cet exemple, l’opération de filtrage).

    * L’histogramme bleu représente l’histogramme après l’opération. 

   L’effet de halo permet de visualiser l’effet d’une opération sur les données.

   ![Effet de Halo](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > L’histogramme bleu semble plus court par rapport au précédent. Cette différence est due à la re-création de compartiments automatique de données dans la nouvelle plage.

2. Pour supprimer le halo, sélectionnez __Modifier__ et décochez __Afficher le halo__.

    ![Options de l’histogramme](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Sélectionnez **OK** pour désactiver l’effet de halo. Réduisez ensuite l’histogramme.

### <a name="remove-columns"></a>Supprimer des colonnes

Dans les données de trajet, chaque ligne représente un événement de collecte de vélo. Pour ce didacticiel, vous avez uniquement besoin des colonnes **starttime** et **start station id**. Pour supprimer les autres colonnes, sélectionnez simultanément ces deux colonnes, cliquez avec le bouton droit sur leur en-tête de colonne, puis sélectionnez **Conserver la colonne**. Les autres colonnes sont supprimées.

![Option Conserver une colonne](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Résumer les données (nombre)

Pour résumer la demande de vélos pendant une période de deux heures, utilisez des colonnes dérivées.

1. Cliquez avec le bouton droit sur la colonne **starttime** et sélectionnez **Dériver une colonne par un exemple**.

    ![Option Dériver des colonnes par l’exemple](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Pour l’exemple, entrez la valeur **Jan 01, 2017 12AM-2AM** pour la première ligne.

    > [!IMPORTANT]
    > Dans l’exemple précédent de dérivation de colonnes, vous avez utilisé plusieurs étapes pour dériver une colonne qui contenait la période de date et d’heure. Dans cet exemple, vous pouvez voir que cette opération peut être effectuée dans une seule étape en fournissant un exemple de la sortie finale.

    > [!NOTE]
    > Vous pouvez donner un exemple par rapport à n’importe quelle ligne. Pour l’exemple, la valeur **Jan 01, 2017 12AM-2AM** est valide pour la première ligne de données.

    ![Exemple de données](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > Sur Mac, suivez cette étape à la place de l’étape 3 :
   >
   > * Accédez à la première cellule qui contient **Jan 01, 2017 1AM-2AM**. Il doit s’agir de la ligne 14. Corrigez la valeur en **Jan 01, 2017 12AM-2AM** puis sélectionnez Entrée. 

3. Attendez que l’application calcule les valeurs par rapport à toutes les lignes. Ce processus peut prendre plusieurs secondes. Une fois l’analyse terminée, utilisez le lien __Passer en revue la ligne suggérée suivante__ pour passer en revue les données.

   ![Analyse terminée avec lien de révision](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Vérifiez que les valeurs calculées sont correctes. Si ce n’est pas le cas, mettez à jour la valeur avec la valeur attendue et appuyez sur Entrée. Attendez ensuite que l’analyse se termine. Effectuez le processus **Passer en revue la ligne suggérée suivante** jusqu’à ce que vous voyiez **Aucune suggestion**. **Aucune suggestion** signifie que l’application a examiné les cas marginaux et qu’elle est satisfaite du programme résumé. Une bonne pratique consiste à effectuer un examen visuel des données transformées avant d’accepter la transformation. 

4. Sélectionnez **OK** pour accepter la transformation. Renommez la colonne nouvellement créée **Date Plage horaire**.

    ![Colonne renommée](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Cliquez avec le bouton droit sur l’en-tête de colonne **starttime** et sélectionnez **Supprimer la colonne**.

6. Pour résumer les données, sélectionnez __Résumer__ dans le menu __Transformer__. Pour créer la transformation, suivez les étapes suivantes :

    * Faites glisser __Date Plage horaire__ et __start station id__ vers le volet **Regroupé par** situé à gauche.

    * Faites glisser __start station id__ vers le volet **Résumer des données** situé à droite.

   ![Options de résumé](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Sélectionnez **OK** pour accepter le résultat du résumé.

## <a name="join-dataflows"></a>Joindre des flux de données

Pour joindre les données météorologiques aux données de trajet, effectuez les étapes suivantes :

1. Sélectionnez __Joindre__ dans le menu __Transformations__.

2. __Tables__ : Sélectionnez **BostonWeather** comme flux de données **gauche** et **201701-hubway-tripdata** comme flux de données **droit**. Pour continuer, sélectionnez **Suivant**.

    ![Sélections de tables](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Colonnes clés__ : Sélectionnez la colonne **Date Plage horaire** dans les deux tables, puis sélectionnez __Suivant__.

    ![Sélections de colonnes clés](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Type de jointure__ : Sélectionnez __Lignes correspondantes__ comme type de jointure, puis sélectionnez __Terminer__.

    ![Type de jointure Lignes correspondantes](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Ce processus crée un nouveau flux de données nommé __Résultat de la jointure__.

## <a name="create-additional-features"></a>Créer des caractéristiques supplémentaires

1. Pour créer une colonne qui contient le jour de la semaine, cliquez avec le bouton droit sur la colonne **Date Plage horaire** et sélectionnez **Dériver une colonne par un exemple**. Utilisez la valeur __Sun__ pour une date qui tombe un dimanche. Par exemple, **Jan 01, 2017 12AM-2AM**. Sélectionnez Entrée, puis **OK**. Renommez cette colonne __Jour de la semaine__.

    ![Créer une nouvelle colonne pour un jour de la semaine](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Pour créer une colonne contenant la plage horaire d’une ligne, cliquez avec le bouton droit sur la colonne **Date Plage horaire** et sélectionnez **Dériver une colonne par un exemple**. Utilisez la valeur **12AM-2AM** pour la ligne contenant **Jan 01, 2017 12AM-2AM**. Sélectionnez Entrée, puis **OK**. Renommez cette colonne **Période**.

    ![Colonne de période](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Pour supprimer les colonnes **Date Hour Range** et **r_Date Hour Range**, appuyez sur Ctrl (Command ⌘ sur Mac) et sélectionnez chaque en-tête de colonne. Cliquez avec le bouton droit et sélectionnez **Supprimer la colonne**.

## <a name="read-data-from-python"></a>Lire les données à partir de Python

Vous pouvez exécuter un package de préparation des données à partir de Python ou PySpark et récupérer le résultat sous forme de **trame de données**.

Pour générer un exemple de script Python, cliquez avec le bouton droit sur __BikeShare Data Prep__ et sélectionnez __Générer le fichier de code d’accès aux données__. L’exemple de fichier Python est créé dans votre **dossier du projet**. Il est également chargé sous un onglet dans Workbench. Le script Python suivant est un exemple de code généré :

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

Pour ce didacticiel, le nom du fichier est `BikeShare Data Prep.py`. Ce fichier est utilisé ultérieurement dans le didacticiel.

## <a name="save-test-data-as-a-csv-file"></a>Enregistrer les données de test dans un fichier CSV

Pour enregistrer le flux de données **Résultat de la jointure** dans un fichier .CSV, vous devez modifier le script `BikeShare Data Prep.py`. 

1. Ouvrez le projet dans Visual Studio Code pour le modifier.

    ![Ouvrir un projet dans Visual Studio Code](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

2. Mettez à jour le script Python dans le fichier `BikeShare Data Prep.py` en utilisant le code suivant :

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

3. Remplacez `Your Azure Storage blob path` par le chemin d’accès du fichier de sortie à créer, à la fois pour la variable `blobfolder` et pour la variable `csvfiles`.

## <a name="create-an-hdinsight-run-configuration"></a>Créer une configuration de série de tests HDInsight

1. Dans Machine Learning Workbench, ouvrez la fenêtre de ligne de commande, sélectionnez le menu **Fichier**, puis **Ouvrir l’invite de commandes**. Votre invite de commandes démarre dans le dossier du projet avec l’invite `C:\Projects\BikeShare>`.

    ![Ouvrir l’invite de commandes](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >Vous devez utiliser la fenêtre de ligne de commande (ouverte à partir de Workbench) pour effectuer les étapes suivantes.

2. Utilisez l’invite de commandes pour vous connecter à Azure. 

   L’application Workbench et l’interface CLI utilisent des caches d’informations d’identification indépendants pendant l’authentification auprès des ressources Azure. Vous ne devez effectuer cette opération qu’une seule fois, jusqu’à ce que le jeton mis en cache expire. La commande `az account list` retourne la liste des abonnements auxquels votre connexion a accès. S’il existe plusieurs options, utilisez la valeur d’ID de l’abonnement souhaité. Choisissez cet abonnement comme compte par défaut à utiliser avec la commande `az account set -s`, puis indiquez la valeur de l’ID d’abonnement. Vérifiez ensuite le paramètre à l’aide de la commande account `show`.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Créez la configuration de série de tests HDInsight. Vous aurez besoin du nom de votre cluster et du mot de passe `sshuser`.

    ```azurecli
    az ml computetarget attach --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password> --type cluster
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> Lorsqu’un projet vide est créé, les configurations de série de tests par défaut sont **local** et **docker**. Cette étape crée une nouvelle configuration de série de test qui est accessible dans Workbench lors de l’exécution des scripts. 

## <a name="run-in-an-hdinsight-cluster"></a>Exécuter dans un cluster HDInsight

Revenez à l’application Machine Learning Workbench pour exécuter votre script dans le cluster HDInsight.

1. Revenez sur l’écran d’accueil de votre projet en sélectionnant l’icône **Accueil** à gauche.

2. Sélectionnez **hdinsight** dans la liste déroulante pour exécuter votre script dans le cluster HDInsight.

3. Sélectionnez **Exécuter**. Le script est soumis sous forme de tâche. Elle passe à l’état __Terminé__lorsque le fichier est écrit à l’emplacement spécifié dans votre conteneur de stockage Azure.

    ![Exécution du script dans HDInsight](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>Remplacer des sources de données

Dans les étapes précédentes, vous avez utilisé les sources de données `201701-hubway-tripdata.csv` et `BostonWeather.csv` pour préparer les données de test. Pour utiliser le package avec les autres fichiers de données de trajet, effectuez les étapes suivantes :

1. Créez une source de données en suivant les étapes indiquées précédemment, en apportant les modifications suivantes au processus :

    * __Sélection de fichiers__ : Lors de la sélection multiple de fichiers, sélectionnez les six fichiers .CSV de données de trajet restants.

    ![Charger les six fichiers restants](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > L’entrée __+5__ indique qu’il existe cinq fichiers supplémentaires en plus de celui répertorié.

    * __Détails des fichiers__ : Affectez à l’option __Promouvoir le mode en-têtes__ la valeur **Tous les fichiers ont les mêmes en-têtes**. Cette valeur indique que chacun des fichiers contient le même en-tête.

    ![Sélection des détails des fichiers](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Enregistrez le nom de cette source de données, car il sera utilisé dans les étapes ultérieures.

2. Sélectionnez l’icône de dossier pour afficher les fichiers inclus dans votre projet. Développez le répertoire __aml\_config__ et sélectionnez le fichier `hdinsight.runconfig`.

    ![Emplacement de hdinsight.runconfig](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

3. Sélectionnez le bouton **Modifier** pour ouvrir le fichier dans Visual Studio Code.

4. Ajoutez les lignes suivantes à la fin du fichier `hdinsight.runconfig`, puis sélectionnez l’icône de disque pour enregistrer le fichier.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Cette modification remplace la source de données d’origine par celle qui contient les six fichiers de données de trajet.

## <a name="save-training-data-as-a-csv-file"></a>Enregistrer les données d’apprentissage dans un fichier CSV

1. Accédez au fichier Python `BikeShare Data Prep.py`, que vous avez modifié précédemment. Renseignez un chemin d’accès au fichier différent pour enregistrer les données d’apprentissage.

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

2. Utilisez le dossier nommé `traindata` pour la sortie des données d’apprentissage.

3. Pour soumettre un nouveau travail, sélectionnez **Exécuter**. Vérifiez que **hdinsight** est sélectionné. Un travail est envoyé avec la nouvelle configuration. La sortie de ce travail correspond aux données d’apprentissage. Ces données sont créées à l’aide des mêmes étapes de préparation des données que vous avez suivies précédemment. Le travail peut prendre quelques instants pour se terminer.


## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>étapes suivantes
Vous avez terminé le didacticiel de préparation des données BikeShare. Dans ce didacticiel, vous avez utilisé les services Machine Learning (préversion) pour apprendre à effectuer les tâches suivantes :
> [!div class="checklist"]
> * Préparer des données de manière interactive avec l’outil de préparation des données Machine Learning.
> * Importer, transformer et créer un jeu de données de test.
> * Générer un package de préparation des données.
> * Exécuter le package de préparation des données à l’aide de Python.
> * Générer un jeu de données d’apprentissage en réutilisant le package de préparation des données pour des fichiers d’entrée supplémentaires.

À présent, découvrez-en plus sur la préparation des données :
> [!div class="nextstepaction"]
> [Guide de l’utilisateur de préparation des données](data-prep-user-guide.md)
