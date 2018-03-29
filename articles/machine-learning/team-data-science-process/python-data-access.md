---
title: Accès à des jeux de données grâce à la bibliothèque cliente Python Machine Learning | Microsoft Docs
description: Installez et utilisez la bibliothèque cliente Python pour accéder et gérer les données d'apprentissage automatique d'Azure en toute sécurité à partir d'un environnement Python local.
services: machine-learning
documentationcenter: python
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 9ab42272-c30c-4b7e-8e66-d64eafef22d0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: 5f0823e07f0e583e32829b664a1d3d463092169e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Accédez aux jeux de données avec Python grâce à la bibliothèque cliente Python d'Azure Machine Learning
L’aperçu de la bibliothèque cliente Python de Microsoft Azure Machine Learning offre un accès sécurisé à vos jeux de données Azure Machine Learning à partir d’un environnement Python local et permet la création et la gestion de jeux de données dans un espace de travail.

Cette rubrique fournit des instructions pour les procédures suivantes :

* installation de la bibliothèque cliente Python de Machine Learning 
* accès et téléchargement des jeux de données, y compris des instructions sur l’obtention d’une autorisation d'accès aux jeux de données Azure Machine Learning depuis votre environnement Python local
* accès aux jeux de données intermédiaires à partir d'expériences
* utilisation de la bibliothèque cliente Python pour énumérer les jeux de données, accès aux métadonnées, lecture du contenu d'un jeu de données, création de nouveaux jeux de données et mise à jour des jeux de données existants

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Configuration requise
La bibliothèque cliente Python a été testée dans les environnements suivants :

* Windows, Mac et Linux
* Python 2.7, 3.3 et 3.4

Il a une dépendance sur les packages suivants :

* requêtes
* python-dateutil
* pandas

Nous vous invitons à utiliser une distribution Python telle qu’[Anaconda](http://continuum.io/downloads#all) ou [Canopy](https://store.enthought.com/downloads/), qui est fournie avec Python, IPython et les trois packages listés ci-dessus installés. Bien que IPython n'est pas formellement requis, il s'agit d'un environnement idéal pour la manipulation et la visualisation interactive des données.

### <a name="installation"></a>Installation de la bibliothèque cliente Python d'Azure Machine Learning
La bibliothèque cliente Python d’Azure Machine Learning doit également être installée pour effectuer les tâches décrites dans cette rubrique. Elle est disponible depuis le [Python Package Index](https://pypi.python.org/pypi/azureml). Pour l'installer dans votre environnement Python, exécutez la commande suivante à partir de votre environnement Python local :

    pip install azureml

Autrement, vous pouvez la télécharger et l'installer à partir des sources sur [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Si vous avez installé git sur votre ordinateur, vous pouvez utiliser pip pour l'installer directement à partir du référentiel git :

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Utilisation des extraits de code Studio pour accéder aux jeux de données
La bibliothèque cliente Python vous offre un accès par programme à vos jeux de données existants à partir des expériences qui ont été exécutées.

Depuis l'interface web Studio, vous pouvez générer des extraits de code qui incluent toutes les informations nécessaires pour télécharger et désérialiser des jeux de données en tant qu'objets DataFrame de Pandas sur votre ordinateur local.

### <a name="security"></a>Sécurité relative à l'accès aux données
Les extraits de code fournis par Studio pour une utilisation avec la bibliothèque cliente Python incluent l'ID de votre d'espace de travail et le jeton d'autorisation. Ceux-ci vous permettent un accès complet à votre espace de travail et doivent être protégés, par exemple avec un mot de passe.

Pour des raisons de sécurité, la fonctionnalité d'extrait de code est uniquement disponible pour les utilisateurs qui ont leur rôle défini en tant que **Propriétaire** de l'espace de travail. Votre rôle s’affiche dans Azure Machine Learning Studio sur la page **UTILISATEURS** sous **Paramètres**.

![Sécurité][security]

Si votre rôle n’est pas défini en tant que **Propriétaire**, vous pouvez demander à être invité à nouveau en tant que propriétaire ou demander au propriétaire de l’espace de travail de vous fournir l’extrait de code.

Pour obtenir le jeton d'autorisation, vous pouvez effectuer l'une des opérations suivantes :

* Demander un jeton à un propriétaire. Les propriétaires peuvent accéder à leurs jetons d'autorisation à partir de la page Paramètres de leur espace de travail dans Studio. Sélectionnez **Paramètres** dans le volet gauche puis cliquez sur **JETONS D’AUTORISATION** pour voir les jetons principaux et secondaires.  Bien que les jetons d'autorisation principaux ou secondaires puissent être utilisés dans l'extrait de code, il est recommandé aux propriétaires de ne partager que les jetons d'autorisation secondaires.

![Jetons d’autorisation](./media/python-data-access/ml-python-access-settings-tokens.png)

* Demander à être promu au rôle de propriétaire.  Pour cela, un propriétaire actuel de l'espace de travail doit tout d'abord vous supprimer de l'espace de travail puis vous y inviter à nouveau en tant que propriétaire.

Une fois que les développeurs ont obtenu l’ID de l’espace de travail et les jetons d’autorisation, ils peuvent accéder à l’espace de travail à l’aide de l’extrait de code, quel que soit leur rôle.

Les jetons d’autorisation sont gérés sur la page **JETONS D’AUTORISATION** sous **PARAMÈTRES**. Vous pouvez les régénérer, mais cette procédure entraîne la révocation de l’accès au jeton précédent.

### <a name="accessingDatasets"></a>Accès aux jeux de données depuis une application Python locale
1. Dans Machine Learning Studio, cliquez sur **JEUX DE DONNÉES** dans la barre de navigation à gauche.
2. Sélectionnez le jeu de données auquel vous souhaitez accéder. Vous pouvez sélectionner un des jeux de données depuis la liste **MES JEUX DE DONNÉES** ou **EXEMPLES**.
3. Dans la barre d’outils inférieure, cliquez sur **Générer un code d’accès aux données**. Ce bouton est désactivé si les données sont dans un format incompatible avec la bibliothèque cliente Python.
   
    ![Groupes de données][datasets]
4. Sélectionnez l'extrait de code dans la fenêtre qui s'affiche et copiez-le dans votre presse-papiers.
   
    ![Code d'accès][dataset-access-code]
5. Collez le code dans le bloc-notes de votre application Python locale.
   
    ![Bloc-notes][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Accès aux jeux de données intermédiaires à partir d'expériences de Machine Learning
Après l'exécution d'une expérience dans Machine Learning Studio, il est possible d'accéder aux jeux de données intermédiaires depuis les nœuds de modules de sortie. Les jeux de données intermédiaires sont des données qui ont été créées et utilisées pour les étapes intermédiaires lorsqu'un outil de modèle a été exécuté.

Les jeux de données intermédiaires sont accessibles tant que le format de données est compatible avec la bibliothèque cliente Python.

Les formats suivants sont pris en charge (ces constantes sont dans la classe `azureml.DataTypeIds` ) :

* Texte brut
* CSV générique
* TSV générique
* CSV générique sans en-tête
* TSV générique sans en-tête

Vous pouvez déterminer le format en pointant sur un nœud de sortie de module. Celui-ci s'affiche avec le nom de nœud dans une infobulle.

Certains des modules, tels que le module [Fractionner][split], ont un format de sortie appelé `Dataset`, qui n’est pas pris en charge par la bibliothèque cliente Python.

![Format de jeu de données][dataset-format]

Vous devez utiliser un module de conversion, tel que [Convertir en CSV][convert-to-csv], afin d’obtenir un format de sortie pris en charge.

![Format CSV générique][csv-format]

Les étapes suivantes proposent un exemple qui créé une expérience, l'exécute et accède au jeu de données intermédiaire.

1. Création d'une nouvelle expérience.
2. Insérez un module **Jeu de données Adult Census Income Binary Classification** .
3. Insérez un module [Fractionner][split] puis connectez son entrée à une sortie de module de jeu de données.
4. Insérez un module [Convertir en CSV][convert-to-csv], puis connectez son entrée à l’une des sorties du module [Fractionner][split].
5. Enregistrez l'expérience, exécutez-la et attendez qu'elle ait fini de s'exécuter.
6. Cliquez sur le nœud de sortie du module [Convertir en CSV][convert-to-csv].
7. Lorsque le menu contextuel s’affiche, sélectionnez **Générer un code d’accès aux données**.
   
    ![Menu contextuel][experiment]
8. Sélectionnez l’extrait de code dans la fenêtre qui s’affiche et copiez-le dans votre presse-papiers.
   
    ![Code d'accès][intermediate-dataset-access-code]
9. Collez le code dans votre bloc-notes.
   
    ![Bloc-notes][ipython-intermediate-dataset]
10. Vous pouvez visualiser les données à l'aide de matplotlib. Cela les affiche dans un histogramme pour la colonne âge :
    
    ![Histogramme][ipython-histogram]

## <a name="clientApis"></a>Utilisation de la bibliothèque cliente Python de Machine Learning pour accéder, lire, créer et gérer des jeux de données
### <a name="workspace"></a>Espace de travail
L'espace de travail est le point d'entrée de la bibliothèque cliente Python. Il fournit la classe `Workspace` avec l'ID de votre espace de travail et le jeton d'autorisation pour créer une instance :

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Énumérer les jeux de données
Pour énumérer tous les jeux de données dans un espace de travail donné :

    for ds in ws.datasets:
        print(ds.name)

Pour énumérer uniquement les jeux de données créés par l'utilisateur :

    for ds in ws.user_datasets:
        print(ds.name)

Pour énumérer uniquement les exemples de jeux de données :

    for ds in ws.example_datasets:
        print(ds.name)

Vous pouvez accéder à un jeu de données par son nom (qui respecte la casse) :

    ds = ws.datasets['my dataset name']

Vous pouvez également y accéder par l'index :

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
Les jeux de données ont des métadonnées, en plus du contenu. (Les jeux de données intermédiaires sont une exception à cette règle et n'ont pas de métadonnées.)

Certaines valeurs de métadonnées sont affectées par l'utilisateur lors de la création :

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

D'autres sont des valeurs affectées par Azure ML :

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consultez la classe `SourceDataset` pour plus d'informations sur les métadonnées disponibles.

### <a name="read-contents"></a>Lire le contenu
Les extraits de code fournis par Machine Learning Studio téléchargent automatiquement et désérialisent le jeu de données vers un objet DataFrame de Pandas. Cette opération est effectuée à l'aide de la méthode `to_dataframe` :

    frame = ds.to_dataframe()

Si vous préférez télécharger les données brutes et procéder vous-même à la désérialisation, cela est possible. Pour le moment, il s'agit de la seule option pour les formats tels que « ARFF » que la bibliothèque cliente Python ne peut pas désérialiser.

Pour lire le contenu en texte :

    text_data = ds.read_as_text()

Pour lire le contenu en binaire :

    binary_data = ds.read_as_binary()

Vous pouvez également ouvrir un simple flux vers le contenu :

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Créer un nouveau jeu de données
La bibliothèque cliente Python vous permet de télécharger des jeux de données depuis votre programme Python. Ces jeux de données sont alors disponibles pour une utilisation dans votre espace de travail.

Si vous avez vos données dans un DataFrame de Pandas, utilisez le code suivant :

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Si vos données sont déjà sérialisées, vous pouvez utiliser :

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

La bibliothèque cliente Python est en mesure de sérialiser une trame de données Pandas aux formats suivants (ces constantes sont dans la classe `azureml.DataTypeIds` ) :

* Texte brut
* CSV générique
* TSV générique
* CSV générique sans en-tête
* TSV générique sans en-tête

### <a name="update-an-existing-dataset"></a>Mettre à jour un jeu de données existant
Si vous essayez de télécharger un nouveau jeu de données avec un nom qui correspond à un jeu de données existant, vous devriez obtenir une erreur de conflit.

Pour mettre à jour un jeu de données existant, vous devez d'abord obtenir la référence d'un jeu de données existant :

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Utilisez ensuite `update_from_dataframe` pour sérialiser et remplacer le contenu du jeu de données sur Azure :

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Si vous souhaitez sérialiser les données dans un format différent, spécifiez une valeur pour le paramètre en option `data_type_id` .

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Vous pouvez éventuellement définir une nouvelle description en spécifiant une valeur pour le paramètre `description` .

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Vous pouvez éventuellement définir un nouveau nom en spécifiant une valeur pour le paramètre `name` . À partir de maintenant, vous allez récupérer le jeu de données uniquement à l'aide du nouveau nom. Le code suivant met à jour les données, le nom et la description.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Les paramètres `data_type_id`, `name` et `description` sont facultatifs. Par défaut, ils indiquent leur valeur précédente. Le paramètre `dataframe` est toujours requis.

Si vos données sont déjà sérialisées, utilisez `update_from_raw_data` au lieu de `update_from_dataframe`. Si vous transmettez simplement `raw_data` au lieu de `dataframe`, cela fonctionne de la même manière.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

