---
title: "Didacticiel BikeShare - Préparation de données avancée avec Azure Machine Learning Workbench"
description: "Didacticiel de préparation de données de bout en bout avec Azure Machine Learning Workbench"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 69f6911a95be382b06313d984f09c7e85aec10df
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>Didacticiel BikeShare : Préparation de données avancée avec Azure Machine Learning Workbench
Les services Azure Machine Learning (préversion) forment une solution d’analytique avancée et de science des données intégrée complète qui permet aux scientifiques des données professionnels de préparer des données, de développer des expérimentations et de déployer des modèles à l’échelle du cloud.

Dans ce didacticiel, vous utilisez les services Azure Machine Learning (préversion) pour apprendre à effectuer les tâches suivantes :
> [!div class="checklist"]
> * Préparer des données de manière interactive avec l’outil de préparation des données Azure Machine Learning
> * Importer, transformer et créer un jeu de données de test
> * Générer un package de préparation des données
> * Exécuter le package de préparation des données à l’aide de Python
> * Générer un jeu de données d’apprentissage en réutilisant le package de préparation des données pour des fichiers d’entrée supplémentaires

> [!IMPORTANT]
> Ce didacticiel prépare uniquement les données, il ne génère pas le modèle de prévision.
>
> Vous pouvez utiliser les données préparées pour former vos propres modèles de prévision. Par exemple, vous pouvez créer un modèle pour prévoir la demande de vélos pendant une période de 2 heures.

## <a name="prerequisites"></a>configuration requise
* Azure Machine Learning Workbench doit être installé localement. Pour plus d’informations, suivez le [Guide de démarrage rapide sur l’installation](quickstart-installation.md).
* Vous devez savoir créer un projet dans Workbench.

## <a name="data-acquisition"></a>Acquisition de données
Ce didacticiel utilise le [jeu de données Boston Hubway](https://s3.amazonaws.com/hubway-data/index.html) et les données météorologiques de Boston diffusées par l’agence [NOAA](http://www.noaa.gov/).

1. Téléchargez les fichiers de données à partir des liens suivants dans votre environnement de développement local. 
   * [Données météorologiques de Boston](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv). 
   * Données de trajet Hubway issues du site web Hubway.

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Décompressez chaque fichier .zip après son téléchargement.

## <a name="learn-about-the-datasets"></a>En savoir plus sur les jeux de données
1. Le fichier __Boston weather__ contient les champs d’informations météo suivants, consignées toutes les heures :
   * DATE
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. Les données __Hubway__ sont organisées dans des fichiers par année et mois. Par exemple, le fichier nommé `201501-hubway-tripdata.zip` contient un fichier .csv contenant les données de janvier 2015. Les données contiennent les champs suivants, chaque ligne représentant un trajet à vélo :

   * Trip Duration (in seconds) [Durée du trajet (en secondes)]
   * Start Time and Date [Date et heure de départ]
   * Stop Time and Date [Date et heure d’arrivée]
   * Start Station Name & ID [Nom et ID de la station de départ]
   * End Station Name & ID [Nom et ID de la station d’arrivée]
   * Bike ID [ID de vélo]
   * User Type (Casual = 24-Hour or 72-Hour Pass user; Member = Annual or Monthly Member) [Type d’utilisateur (Occasionnel = utilisateur avec carte 24 ou 72 heures ; Membre = adhésion annuelle ou mensuelle)]
   * Zip Code [Code postal] (si l’utilisateur est membre)
   * Gender [Sexe] (indiqué par le membre)

## <a name="create-a-new-project"></a>Création d'un projet
1. Lancez **Azure Machine Learning Workbench** à partir de votre menu Démarrer ou du lanceur.

2. Créez un projet Azure Machine Learning.  Cliquez sur le bouton **+** dans la page **Projets**, ou bien sur **Fichier** > **Nouveau**.
   - Utilisez le modèle de **projet vide**.
   - Nommez votre projet **BikeShare**. 

## <a id="newdatasource"></a>Créer une source de données

1. Créez une source de données. Cliquez sur le bouton **Données** (icône représentant un cylindre) dans la barre d’outils à gauche. La **vue de données** s’affiche.

   ![Image de l’onglet de la vue de données](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Ajoutez une source de données. Sélectionnez l’icône **+**, puis sélectionnez **Ajouter une source de données**.

   ![Image de l’entrée Ajouter une source de données](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Ajouter les données météorologiques

1. **Magasin de données** : Sélectionnez le magasin de données qui contient les données. Étant donné que vous utilisez des fichiers, sélectionnez **Fichier(s)/Répertoire**. Sélectionnez **Suivant** pour continuer.

   ![Image de l’entrée Fichier(s)/Répertoire](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Sélection de fichiers** : Ajoutez les données météorologiques. Recherchez et sélectionnez le fichier `BostonWeather.csv` que vous avez précédemment téléchargé. Cliquez sur **Suivant**.

   ![Image de la sélection de fichiers avec BostonWeather.csv sélectionné](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **Détails du fichier** : Vérifiez le schéma de fichier détecté. Azure Machine Learning Workbench analyse les données dans le fichier et en déduit le schéma à utiliser.

   ![Image des détails du fichier](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > Workbench ne détecte pas le bon schéma dans certains cas. Vous devez toujours vérifier que les paramètres sont corrects pour votre jeu de données. Vérifiez que les données météorologiques ont les valeurs suivantes :
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

4. **Types de données** : Passez en revue les types de données qui sont détectés automatiquement. Azure Machine Learning Workbench analyse les données dans le fichier et en déduit les types de données à utiliser.

   Pour ces données, modifiez la valeur `DATA TYPE` de toutes les colonnes par `String`.

   > [!NOTE]
   > `String` est utilisé pour mettre en évidence les fonctionnalités de Workbench plus loin dans ce didacticiel. 

   ![Image des types de données](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   Pour continuer, sélectionnez __Suivant__. 

5. **Échantillonnage** : Pour créer un schéma d’échantillonnage, sélectionnez le bouton **+ Nouveau**. Sélectionnez la nouvelle ligne __Top 10000__ qui s’ajoute, puis sélectionnez __Modifier__. Définissez l’__exemple de stratégie__ sur **Fichier complet**, puis sélectionnez **Appliquer**.

   ![Image de l’ajout d’une nouvelle stratégie d’échantillonnage](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   Pour utiliser la stratégie __Fichier complet__, sélectionnez l’entrée __Fichier complet__, puis sélectionnez __Définir comme actif__. Une étoile apparaît en regard de __Fichier complet__ pour indiquer qu’il s’agit de la stratégie active.

   ![Image de la stratégie active Fichier complet](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Pour continuer, sélectionnez **Suivant**.

6. **Colonne de chemin** : La section __Colonne de chemin__ vous permet d’inclure le chemin de fichier complet en tant que colonne dans les données importées. Sélectionnez __Ne pas inclure la colonne de chemin__.

   > [!TIP]
   > Inclure le chemin en tant que colonne s’avère utile si vous importez un dossier comprenant de nombreux fichiers portant des noms différents. Cela s’avère également utile si les noms de fichier contiennent des informations que vous voulez extraire plus tard.

   ![Image de l’option consistant à ne pas inclure la colonne de chemin](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Terminer** : Pour terminer la création de la source de données, sélectionnez le bouton **Terminer**.

    Un nouvel onglet de source de données nommé __BostonWeather__ s’ouvre. Un exemple des données s’affiche dans une grille. L’exemple s’appuie sur le schéma d’échantillonnage actif spécifié précédemment.

    Notez que le volet **Étapes** du côté droit de l’écran affiche les actions individuelles effectuées lors de la création de cette source de données.

   ![Image montrant la source de données, l’exemple et les étapes](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Afficher les métriques de la source de données

Sélectionnez le bouton __Métriques__ en haut à gauche de la vue grille de l’onglet. Cette vue affiche la distribution et d’autres statistiques agrégées des données échantillonnées.

![Image des métriques affichées](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Pour configurer la visibilité des statistiques, utilisez la liste déroulante **Choisir une métrique**. Cochez et décochez les métriques pour modifier la grille.

Pour retourner dans la __vue de données__, sélectionnez __Données__ dans l’angle supérieur gauche de la page.

## <a name="add-data-source-to-data-preparation-package"></a>Ajouter la source de données au package de préparation des données

1. Sélectionnez __Préparer__ pour commencer la préparation des données. 

2. Lorsque vous y êtes invité, entrez un nom pour le package de préparation des données, comme `BikeShare Data Prep`. 

3. Sélectionnez __OK__ pour continuer.

   ![Image de la boîte de dialogue de préparation](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Un nouveau package nommé **BikeShare Data Prep** apparaît sous la section __Préparation des données__ de l’onglet __Données__. 

   Pour afficher le package, sélectionnez cette entrée. 

5. Sélectionnez le bouton **>>** pour développer l’affichage des __flux de données__ contenus dans le package. Dans cet exemple, __BostonWeather__ est le seul flux de données.

   > [!IMPORTANT]
   > Un package peut contenir plusieurs flux de données.

   ![Image des flux de données contenus dans le package](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrer les données par valeur
1. Pour filtrer les données, cliquez avec le bouton droit sur une cellule contenant une certaine valeur, puis sélectionnez __Filtrer__ et le type de filtre.

2. Pour ce didacticiel, sélectionnez une cellule qui contient la valeur `FM-15`, puis définissez le filtre sur **Est égal à**.  À présent, les données sont filtrées pour ne renvoyer que les lignes où la valeur de __REPORTTYPE__ est `FM-15`.

   ![Image de la boîte de dialogue de filtrage](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 est un type de rapport d’observation météorologique pour l’aviation (METAR). Les rapports FM 15 sont observés de manière empirique afin d’être les plus complets possible, avec peu de données manquantes.

## <a name="remove-a-column"></a>Supprimer une colonne

Vous n’avez plus besoin de la colonne __REPORTTYPE__. Cliquez avec le bouton droit sur l’en-tête de colonne et sélectionnez **Supprimer une colonne**.

   ![Image de l’option de suppression d’une colonne](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Modifier les types de données et supprimer des erreurs
1. Appuyer sur __Ctrl (Command ⌘ sur Mac)__ pendant la sélection d’en-têtes de colonne vous permet de sélectionner plusieurs colonnes à la fois. Utilisez cette technique pour sélectionner les en-têtes de colonne suivants :
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **Cliquez avec le bouton droit** sur l’un des en-têtes de colonne sélectionnés, puis sélectionnez **Convertir le champ en type numérique**. Cette opération convertit le type de données de ces colonnes en type numérique.

   ![Conversion de plusieurs colonnes en type numérique](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtrez les valeurs d’erreur. Certaines colonnes ont des problèmes de conversion des types de données. Ce problème est indiqué par la couleur rouge dans la __barre de qualité des données__ de la colonne.

   Pour supprimer les lignes qui contiennent des erreurs, cliquez avec le bouton droit sur l’en-tête de colonne **HOURLYDRYBULBTEMPF**. Sélectionnez **Filtrer la colonne**. Pour l’option **Je veux**, utilisez la valeur par défaut **Conserver les lignes**. Modifiez la valeur indiquée dans la liste déroulante **Conditions** pour sélectionner **n’est pas une erreur**. Sélectionnez **OK** pour appliquer le filtre.

4. Pour éliminer les lignes d’erreur restantes dans les autres colonnes, répétez ce processus de filtrage pour les colonnes **HOURLYRelativeHumidity** et **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Utiliser des transformations par un exemple

Pour utiliser les données dans une prévision par bloc de deux heures, vous devez calculer les conditions météo moyennes pendant des périodes de deux heures. Pour ce faire, vous pouvez utiliser les actions suivantes :

* Fractionnez la colonne **DATE** dans des colonnes **Date** et **Time** distinctes. Consultez la section suivante pour connaître les étapes détaillées.

* Dérivez une colonne **Hour_Range** à partir de la colonne **Time**. Consultez la section suivante pour connaître les étapes détaillées.

* Dérivez une colonne **Date\_Hour\_Range** à partir des colonnes **DATE** et **Hour_Range**. Consultez la section suivante pour connaître les étapes détaillées.

### <a name="split-column-by-example"></a>Fractionner la colonne par un exemple

1. Fractionnez la colonne **DATE** dans des colonnes de date et d’heure distinctes. Cliquez avec le bouton droit sur l’en-tête de colonne **DATE** et sélectionnez **Fractionner la colonne par un exemple**.

   ![Image de l’entrée Fractionner la colonne par un exemple](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning Workbench identifie automatiquement un délimiteur significatif et crée deux colonnes en fractionnant les données dans des valeurs de date et d’heure. 

3. Sélectionnez __OK__ pour accepter les résultats de l’opération de fractionnement.

   ![Image des colonnes fractionnées DATE_1 et DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Dériver une colonne par un exemple

1. Pour dériver une plage de deux heures, cliquez avec le bouton droit sur l’en-tête de colonne __DATE\_2__ et sélectionnez **Dériver une colonne par un exemple**.

   ![Image de l’entrée Dériver une colonne par un exemple](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Une nouvelle colonne vide est ajoutée avec des valeurs Null.

2. Cliquez dans la première cellule vide de la nouvelle colonne. Donnez un exemple de la plage de temps souhaitée en tapant `12AM-2AM` dans la nouvelle colonne, puis appuyez sur Entrée.

   ![Image de la nouvelle colonne présentant une valeur 12AM-2AM](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure Machine Learning Workbench synthétise un programme basé sur les exemples que vous fournissez et applique le même programme sur les lignes restantes. Toutes les autres lignes sont automatiquement renseignées en fonction de l’exemple que vous avez fourni. Workbench analyse également vos données et tente d’identifier les cas marginaux. 
  
3. Le texte **Analyse des données** au-dessus de la grille indique que Workbench tente de détecter les cas marginaux. Une fois l’opération terminée, l’état passe à **Passer en revue la ligne suggérée suivante** ou **Aucune suggestion**. Dans cet exemple, **Passer en revue la ligne suggérée suivante** est retourné.

4. Pour passer en revue les modifications suggérées, sélectionnez **Passer en revue la ligne suggérée suivante**. La cellule que vous devez passer en revue et corriger (si besoin) est mise en surbrillance dans l’affichage.

   ![Image de révision de ligne](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Sélectionnez __OK__ pour accepter la transformation.
 
5. Vous revenez à la grille des données de __BostonWeather__. Désormais, la grille contient les trois colonnes ajoutées précédemment.

   ![Image de la grille avec les lignes ajoutées](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  Toutes les modifications apportées sont conservées dans le volet **Étapes**. Accédez à l’étape que vous avez créée dans le volet **Étapes**, cliquez sur la flèche vers le bas, puis sélectionnez **Modifier**. La fenêtre avancée **Dériver une colonne par un exemple** s’affiche. Tous vos exemples y sont conservés. Vous pouvez également ajouter manuellement des exemples en double-cliquant sur une ligne dans la grille ci-dessous. Sélectionnez **Annuler** pour revenir à la grille principale sans appliquer les modifications. Vous pouvez également accéder à cette vue en sélectionnant **Mode avancé** quand vous effectuez une transformation **Dériver une colonne par un exemple**.

6. Pour renommer la colonne, double-cliquez sur son en-tête et tapez **Plage horaire**. Appuyez sur **Entrée** pour enregistrer la modification.

   ![Renommage de colonne](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Pour dériver la plage de dates et heures, sélectionnez les colonnes **Date\_1** et **Plage horaire**, cliquez avec le bouton droit et sélectionnez **Dériver une colonne par un exemple**.

   ![Dérivation de colonne par un exemple](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Tapez `Jan 01, 2015 12AM-2AM` comme exemple par rapport à la première ligne et appuyez sur **Entrée**.

   Workbench détermine la transformation en fonction de l’exemple que vous fournissez. Dans cet exemple, le résultat est que le format de la date est modifié et concaténé avec la fenêtre de deux heures.

   ![Image de l’exemple Jan 01, 2015 12AM-2AM](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)


8. Attendez que l’état passe de **Analyse des données** à **Passer en revue la ligne suggérée suivante**. L’attente peut durer plusieurs secondes. Sélectionnez le lien d’état pour accéder à la ligne suggérée. 

   ![Image de la ligne suggérée à passer en revue](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   La ligne a une valeur Null, car la valeur de date source peut être soit jj/mm/aaaa, soit mm/jj/aaaa. Tapez la valeur correcte de `Jan 13, 2015 2AM-4AM` et appuyez sur **Entrée**. Workbench utilise les deux exemples pour améliorer la dérivation des lignes restantes.

   ![Image de données correctement formatées](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Sélectionnez **OK** pour accepter la transformation.

   ![Image de la grille de la transformation terminée](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Vous pouvez utiliser le mode avancé de **Dériver une colonne par un exemple** pour cette étape en cliquant sur la flèche vers le bas dans le volet **Étapes**. Dans la grille de données, des cases à cocher se trouvent en regard des noms de colonne **DATE\_1** et **Plage horaire**. Décochez celle située à côté de la colonne **Plage horaire** pour voir comment cela modifie la sortie. En l’absence de colonne **Plage horaire**en tant qu’entrée, la valeur **12AM-2AM** est traitée comme une constante et ajoutée aux valeurs dérivées. Sélectionnez **Annuler** pour revenir à la grille principale sans appliquer les modifications.

10. Pour renommer la colonne, double-cliquez sur son en-tête. Remplacez le nom par **Date Plage horaire**, puis appuyez sur **Entrée**.

11. Sélectionnez simultanément les colonnes **DATE**, **DATE\_1**, **DATE\_2** et **Plage horaire**. Cliquez avec le bouton droit et sélectionnez **Supprimer la colonne**.

## <a name="summarize-data-mean"></a>Résumer les données (moyenne)

L’étape suivante consiste à résumer les conditions météo en prenant la moyenne des valeurs, regroupées par plage horaire.

1. Sélectionnez la colonne **Date Plage horaire**, puis sélectionnez **Résumer** dans le menu **Transformations**.

    ![Menu Transformations](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Pour résumer les données, vous faites glisser des colonnes de la grille située au bas de la page vers les volets situés en haut à gauche et à droite. Le volet gauche contient le texte **Faites glisser les colonnes ici pour regrouper les données**. Le volet droit contient le texte **Faites glisser les colonnes ici pour résumer les données**. 

    Faites glisser la colonne **Date Plage horaire** de la grille située en bas vers le volet gauche. Faites glisser **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** et **HOURLYWindSpeed** vers le volet droit. 

    Dans le volet droit, sélectionnez **Moyenne** comme mesure **Agrégat** pour chaque colonne. Cliquez sur **OK** pour effectuer la synthèse.

   ![Image de l’écran des données résumées](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>Transformer un flux de données à l’aide d’un script

Remplacer les données situées dans les colonnes numériques par une plage de 0-1 permet à certains modèles de converger rapidement. Pour le moment, il n’existe aucune transformation intégrée pour effectuer cette transformation de façon générique, mais un script Python peut être utilisé pour effectuer cette opération.

1. Dans le menu **Transformation**, sélectionnez **Transformer le flux de données**.

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
    
    ![Boîte de dialogue du script de transformation du flux de données](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Sélectionnez __OK__ pour utiliser le script. Les colonnes numériques dans la grille contiennent désormais des valeurs comprises dans la plage 0-1.

    ![Grille contenant des valeurs comprises entre 0 et 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Vous avez terminé la préparation des données météorologiques. Ensuite, préparez les données de trajet.

## <a name="load-trip-data"></a>Charger les données de trajet

1. Pour importer le fichier `201701-hubway-tripdata.csv`, utilisez les étapes indiquées dans la section [Créer une source de données](#newdatasource). Pendant le processus d’importation, utilisez les options suivantes :

    * __Schéma d’échantillonnage__ : **Fichier complet**. Activez l’exemple. 
    * __Type de données__ : Acceptez les valeurs par défaut.

2. Après avoir importé les données, sélectionnez le bouton __Préparer__ pour commencer à préparer les données. Sélectionnez le package **BikeShare Data Prep.dprep** existant, puis sélectionnez __OK__.

    Ce processus ajoute un **flux de données** au fichier de **préparation des données** existant plutôt que d’en créer un nouveau.

    ![Image de la sélection du package existant](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Une fois la grille chargée, développez __FLUX DE DONNÉES__. Il existe désormais deux flux de données : **BostonWeather** et **201701-hubway-tripdata**. Sélectionnez l’entrée **201701-hubway-tripdata**.

    ![Image de l’entrée 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>Utiliser l’inspecteur de carte

Pour préparer les données, il existe de nombreuses visualisations utiles appelées **inspecteurs** pour les données de chaîne, numériques et géographiques, qui facilitent la compréhension des données et l’identification des valeurs hors norme. Pour utiliser l’inspecteur de carte, effectuez les étapes suivantes :

1. Sélectionnez plusieurs colonnes **start station latitude** et **start station longitude**. Cliquez avec le bouton droit sur l’une des colonnes, puis sélectionnez **Carte**.

    > [!TIP]
    > Pour activer la sélection multiple, maintenez la touche __Ctrl (Command ⌘ sur Mac)__ enfoncée et sélectionnez l’en-tête de chaque colonne.

    ![Image de la visualisation de la carte](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Pour agrandir la carte, sélectionnez l’icône **Agrandir**. Pour ajuster la carte à la fenêtre, sélectionnez l’icône **E** située dans l’angle supérieur gauche de la visualisation.

    ![Image agrandie](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Cliquez sur le bouton **Réduire** pour revenir à la grille.

## <a name="use-column-statistics-inspector"></a>Utiliser l’inspecteur de statistiques de colonne

Pour utiliser l’inspecteur de statistiques de colonne, cliquez avec le bouton droit sur la colonne __tripduration__ et sélectionnez __Statistiques de colonne__.

![Entrée Statistiques de colonne](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Ce processus ajoute une nouvelle visualisation intitulée __Statistiques tripduration__ dans le volet __INSPECTEURS__.

![Image de l’inspecteur de statistiques tripduration](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> La valeur maximale de la durée du trajet est **961 814 minutes**, ce qui correspond à environ deux ans. Apparemment, il existe des valeurs hors norme dans le jeu de données.

## <a name="use-histogram-inspector"></a>Utiliser l’inspecteur d’histogramme

Pour essayer d’identifier les valeurs hors norme, cliquez avec le bouton droit sur la colonne __tripduration__ et sélectionnez __Histogramme__.

![Inspecteur d’histogramme](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

L’histogramme est inutile, car les valeurs hors norme sont illisibles dans le graphique.

## <a name="add-column-using-script"></a>Ajouter une colonne à l’aide d’un script

1. Cliquez avec le bouton droit sur la colonne **tripduration** et sélectionnez **Ajouter une colonne (script)**.

    ![Image du menu Ajouter une colonne (script)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. Dans la boîte de dialogue __Ajouter une colonne (script)__, utilisez les valeurs suivantes :

    * __Nouveau nom de colonne__ : logtripduration
    * __Insérer cette nouvelle colonne après__ : tripduration
    * __Code de la nouvelle colonne__ : `math.log(row.tripduration)`
    * __Type de bloc de code__ : Expression

   ![Boîte de dialogue Ajouter une colonne (script)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Sélectionnez __OK__ pour ajouter la colonne **logtripduration**.

4. Cliquez avec le bouton droit sur la colonne et sélectionnez **Histogramme**.

    ![Histogramme de la colonne logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  Visuellement, cet histogramme semble être une distribution normale avec une fin anormale.

## <a name="use-advanced-filter"></a>Utiliser un filtre avancé

L’utilisation d’un filtre sur les données permet de mettre à jour les inspecteurs avec la nouvelle distribution. Cliquez avec le bouton droit sur la colonne **logtripduration** et sélectionnez **Filtrer la colonne**. Dans la boîte de dialogue __Modifier__, utilisez les valeurs suivantes :

* __Filtrer cette colonne numérique__ : logtripduration
* __Je veux__ : Conserver les lignes
* __Quand__ : Une ou plusieurs des conditions ci-dessous ont la valeur True (OU logique)
* __Si cette colonne__ : est inférieure à
* __La valeur__ : 9

![Options de filtre](media/tutorial-bikeshare-dataprep/loftripfilter.png)

Sélectionnez __OK__ pour appliquer le filtre.

![Histogrammes mis à jour après application du filtre](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>Effet de halo

1. Agrandissez l’histogramme **logtripduration**. Un histogramme bleu se superpose sur un histogramme gris. Cet affichage est appelé **effet de halo** :

    * L’**histogramme gris** représente la distribution avant l’opération (dans cet exemple, l’opération de filtrage).
    * L’**histogramme bleu** représente l’histogramme après l’opération. 

   L’effet de halo permet de visualiser l’effet d’une opération sur les données.

   ![Image de l’effet de halo](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Remarquez que l’histogramme bleu semble plus court par rapport au précédent. Cela est dû à la re-création de compartiments automatique de données dans la nouvelle plage.

2. Pour supprimer le halo, sélectionnez __Modifier__ et décochez __Afficher le halo__.

    ![Options de l’histogramme](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Sélectionnez **OK** pour désactiver l’effet de halo, puis réduisez l’histogramme.

### <a name="remove-columns"></a>Supprimer des colonnes

Dans les données de trajet, chaque ligne représente un événement de collecte de vélo. Pour ce didacticiel, vous avez uniquement besoin des colonnes **starttime** et **start station id**. Supprimez les autres colonnes en sélectionnant simultanément ces deux colonnes, cliquez avec le bouton droit sur leur en-tête de colonne, puis sélectionnez **Conserver la colonne**. Les autres colonnes sont supprimées.

![Image de l’option Conserver la colonne](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Résumer les données (nombre)

Pour résumer la demande de vélos pendant une période de 2 heures, utilisez des colonnes dérivées.

1. Cliquez avec le bouton droit sur la colonne **starttime** et sélectionnez **Dériver une colonne par un exemple**

    ![Image de l’option Dériver une colonne par un exemple](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Pour l’exemple, entrez la valeur `Jan 01, 2017 12AM-2AM` pour la première ligne.

    > [!IMPORTANT]
    > Dans l’exemple précédent de dérivation de colonnes, vous avez utilisé plusieurs étapes pour dériver une colonne qui contenait la période de date et d’heure. Dans cet exemple, vous pouvez voir que cette opération peut être effectuée dans une seule étape en fournissant un exemple de la sortie finale.

    > [!NOTE]
    > Vous pouvez donner un exemple par rapport à n’importe quelle ligne. Pour cet exemple, la valeur `Jan 01, 2017 12AM-2AM` est valide pour la première ligne de données.

    ![Image de l’exemple de données](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)
   
3. Attendez que l’application calcule les valeurs par rapport à toutes les lignes. L’attente peut durer plusieurs secondes. Une fois l’analyse terminée, utilisez le lien __Passer en revue la ligne suggérée suivante__ pour passer en revue les données.

   ![Image de l’analyse terminée avec un lien de passage en revue](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Vérifiez que les valeurs calculées sont correctes. Si ce n’est pas le cas, mettez à jour la valeur avec la valeur attendue et appuyez sur Entrée. Attendez ensuite que l’analyse se termine. Effectuez le processus **Passer en revue la ligne suggérée suivante** jusqu’à ce que vous voyiez **Aucune suggestion**. Une fois que vous voyez **Aucune suggestion**, l’application a examiné les cas marginaux et elle est satisfaite du programme résumé. Une bonne pratique consiste à effectuer un examen visuel des données transformées avant d’accepter la transformation. 

4. Sélectionnez **OK** pour accepter la transformation. Renommez la colonne nouvellement créée **Date Plage horaire**.

    ![Image de la colonne renommée](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Cliquez avec le bouton droit sur l’en-tête de colonne **starttime** et sélectionnez **Supprimer la colonne**.

6. Pour résumer les données, sélectionnez __Résumer__ dans le menu __Transformer__. Pour créer la transformation, effectuez les actions suivantes :

    * Faites glisser __Date Plage horaire__ et __start station id__ vers le volet (de regroupement) situé à gauche.
    * Faites glisser __start station id__ vers le volet (de résumé des données) situé à droite.

   ![Image des options de résumé](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Sélectionnez **OK** pour accepter le résultat du résumé.

## <a name="join-dataflows"></a>Joindre des flux de données

Pour joindre les données météorologiques aux données de trajet, effectuez les étapes suivantes :

1. Sélectionnez __Joindre__ dans le menu __Transformations__.

2. __Tables__ : Sélectionnez **BostonWeather** comme flux de données gauche et **201701-hubway-tripdata** comme flux de données droit. Pour continuer, sélectionnez **Suivant**.

    ![Image des sélections de tables](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Colonnes clés__ : Sélectionnez la colonne **Date Plage horaire** dans les deux tables, puis sélectionnez __Suivant__.

    ![Image de la jointure sur les colonnes clés](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Type de jointure__ : Sélectionnez __Lignes correspondantes__ comme type de jointure, puis sélectionnez __Terminer__.

    ![Type de jointure Lignes correspondantes](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Ce processus crée un nouveau flux de données nommé __Résultat de la jointure__.

## <a name="create-additional-features"></a>Créer des caractéristiques supplémentaires

1. Pour créer une colonne qui contient le jour de la semaine, cliquez avec le bouton droit sur la colonne **Date Plage horaire** et sélectionnez **Dériver une colonne par un exemple**. Utilisez la valeur __Sun__ pour une date qui tombe un dimanche. Par exemple, **Jan 01, 2017 12AM-2AM**. Appuyez sur **Entrée**, puis sélectionnez **OK**. Renommez cette colonne __Jour de la semaine__.

    ![Image de la création d’une colonne qui contient le jour de la semaine](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Pour créer une colonne contenant la plage horaire d’une ligne, cliquez avec le bouton droit sur la colonne **Date Plage horaire** et sélectionnez **Dériver une colonne par un exemple**. Utilisez la valeur **12AM-2AM** pour la ligne contenant **Jan 01, 2017 12AM-2AM**. Appuyez sur **Entrée**, puis sélectionnez **OK**. Renommez cette colonne **Période**.

    ![Image de la colonne Période](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Pour supprimer les colonnes **Date Hour Range** et **rDate Hour Range**, appuyez sur **Ctrl (Command ⌘ sur Mac)** et sélectionnez chaque en-tête de colonne. Cliquez avec le bouton droit et sélectionnez **Supprimer la colonne**.

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

Pour enregistrer le flux de données **Résultat de la jointure** dans un fichier .CSV, vous devez modifier le script `BikeShare Data Prep.py`. Mettez à jour le script Python en utilisant le code suivant :

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

Sélectionnez **Exécuter** en haut de l’écran. Le script est envoyé en tant que **travail** sur l’ordinateur local. Une fois que l’état du travail devient __Terminé__, le fichier est écrit à l’emplacement spécifié.

## <a name="substitute-data-sources"></a>Remplacer des sources de données

Dans les étapes précédentes, vous avez utilisé les sources de données `201701-hubway-tripdata.csv` et `BostonWeather.csv` pour préparer les données de test. Pour utiliser le package avec les autres fichiers de données de trajet, effectuez les étapes suivantes :

1. Créez une **source de données** en suivant les étapes indiquées précédemment, en apportant les modifications suivantes au processus :

    * __Sélection de fichiers__ : Lors de la sélection multiple de fichiers, sélectionnez les six fichiers .CSV de données de trajet restants.

        ![Charger les six fichiers restants](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > L’entrée __+5__ indique qu’il existe cinq fichiers supplémentaires en plus de celui répertorié.

    * __Détails des fichiers__ : Affectez à l’option __Promouvoir le mode en-têtes__ la valeur **Tous les fichiers ont les mêmes en-têtes**. Cette valeur indique que chacun des fichiers contient le même en-tête.

        ![Sélection des détails des fichiers](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Enregistrez le nom de cette source de données, car il sera utilisé dans les étapes ultérieures.

2. Sélectionnez l’icône de dossier pour afficher les fichiers inclus dans votre projet. Développez le répertoire __aml\_config__ et sélectionnez le fichier `local.runconfig`.

    ![Image de l’emplacement de local.runconfig](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. Ajoutez les lignes suivantes à la fin du fichier `local.runconfig`, puis sélectionnez l’icône de disque pour enregistrer le fichier.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Cette modification remplace la source de données d’origine par celle qui contient les six fichiers de données de trajet.

## <a name="save-training-data-as-a-csv-file"></a>Enregistrer les données d’apprentissage dans un fichier CSV

Accédez au fichier Python `BikeShare Data Prep.py` que vous avez modifié précédemment et indiquez un autre chemin de fichier pour enregistrer les données d’apprentissage.

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

Pour envoyer un nouveau travail, utilisez l’icône **Exécuter** située en haut de la page. Un **travail** est envoyé avec la nouvelle configuration. La sortie de ce travail correspond aux données d’apprentissage. Ces données sont créées à l’aide des mêmes étapes de préparation des données que vous avez créées précédemment. Quelques minutes peuvent être nécessaires pour terminer le travail.

## <a name="next-steps"></a>étapes suivantes
Vous avez terminé le didacticiel de préparation des données BikeShare. Dans ce didacticiel, vous avez utilisé les services Azure Machine Learning (préversion) pour apprendre à effectuer les tâches suivantes :
> [!div class="checklist"]
> * Préparer des données de manière interactive avec l’outil de préparation des données Azure Machine Learning
> * Importer, transformer et créer un jeu de données de test
> * Générer un package de préparation des données
> * Exécuter le package de préparation des données à l’aide de Python
> * Générer un jeu de données d’apprentissage en réutilisant le package de préparation des données pour des fichiers d’entrée supplémentaires

À présent, découvrez-en plus sur la préparation des données :
> [!div class="nextstepaction"]
> [Guide de l’utilisateur de préparation des données](data-prep-user-guide.md)
