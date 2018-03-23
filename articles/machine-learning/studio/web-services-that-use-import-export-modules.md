---
title: Utiliser Importer/Exporter des données dans les services web Azure Machine Learning | Microsoft Docs
description: Découvrez comment utiliser les modules Importer des données et Exporter des données pour envoyer et recevoir des données à partir d’un service web.
services: machine-learning
documentationcenter: ''
author: aashishb
ms.author: aashishb
manager: hjerez
editor: ''
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 310ace274649faab3c39ea89dafd2f29dea44109
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Déploiement de services web Azure ML utilisant les modules d’importation et d’exportation des données

Lorsque vous créez une expérience prédictive, vous ajoutez généralement une entrée et une sortie de service web. Lorsque vous déployez l’expérience, les consommateurs peuvent envoyer et recevoir des données à partir du service web via les entrées et sorties. Pour certaines applications, les données d’un consommateur peuvent être disponibles à partir d’un flux de données ou figurer déjà dans une source de données externe tels que le stockage d’objets blob Azure. Dans ces cas, elles n’ont pas besoin de lire ni d’écrire les données en utilisant des entrées et sorties de service web. Au lieu de cela, elles peuvent utiliser le Service d’exécution de lots (BES, Batch Execution Service) pour lire les données à partir de la source de données à l’aide d’un module Importer des données et écrire les résultats de la notation dans un autre emplacement de données à l’aide d’un module d’exporter de données.

Les modules Importer des données et Exporter des données peuvent lire et écrire dans divers emplacements de données, par exemple une URL web via HTTP, une requête Hive, une base de données SQL Azure, un stockage de table Azure, un stockage d’objets blob Azure, un flux de données ou une base de données SQL locale.

Cette rubrique utilise l’exemple « Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset » et suppose que le jeu de données a déjà été chargé dans une table SQL Azure nommée censusdata.

## <a name="create-the-training-experiment"></a>Créer l’expérience de formation
Lorsque vous ouvrez l’exemple « Sample 5: Train, Test, Evaluate for Binary Classification: Adult Dataset », celui-ci utilise le jeu de données Adult Census Income Binary Classification. L’expérience dans la zone de dessin ressemblera à l’image suivante :

![Configuration initiale de l’expérience.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Pour lire les données à partir de la table SQL Azure :

1. Supprimez le module de jeu de données.
2. Dans la zone de recherche des composants, tapez import.
3. Dans la liste des résultats, ajoutez un module *Importer des données* à la zone de dessin de l’expérience.
4. Connectez la sortie du module *Importer des données* à l’entrée du module *Nettoyer les données manquantes*.
5. Dans le panneau Propriétés, sélectionnez **Base de données SQL Azure** in the **Source de données** .
6. Dans les champs **Nom du serveur de base de données**, **Nom de la base de données**, **Nom d’utilisateur** et **Mot de passe** saisissez les informations appropriées pour votre base de données.
7. Dans le champ de requête de base de données, entrez la requête suivante.
   
     select [age],
   
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
8. En bas de la zone de dessin de l’expérience, cliquez sur **Exécuter**.

## <a name="create-the-predictive-experiment"></a>Créer l’expérience prédictive
Maintenant, vous devez configurer l’expérience prédictive à partir de laquelle déployer votre service web.

1. En bas de la zone de dessin de l’expérience, cliquez sur **Configurer le service web** puis sélectionnez **Service web prédictif [recommandé]**.
2. Supprimez l*’entrée du service web* et les *modules de sortie du service web* de l’expérience prédictive. 
3. Dans la zone de recherche des composants, tapez export.
4. Dans la liste des résultats, ajoutez un module *Exporter des données* à la zone de dessin de l’expérience.
5. Connectez la sortie du module *Noter le modèle* à l’entrée du module *Exporter des données*. 
6. Dans le panneau Propriétés, sélectionnez **Base de données SQL Azure** dans la liste déroulante de destination des données.
7. Dans les champs **Nom du serveur de base de données**, **Nom de la base de données**, **Nom de compte d’utilisateur du serveur** et **Mot de passe de compte d’utilisateur du serveur**, saisissez les informations appropriées pour votre base de données.
8. Dans le champ **Liste de colonnes séparées par des virgules à enregistrer** , tapez Étiquettes notées.
9. Dans le **champ du nom de la table de données**, tapez dbo.ScoredLabels. Si la table n’existe pas, elle est créée lors de l’exécution de l’expérience ou lors de l’appel du service web.
10. Dans le champ **Liste des colonnes de table de données séparées par des virgules** , tapez Étiquettes notées.

Lorsque vous écrivez une application qui appelle le service web final, vous pouvez spécifier une autre requête d’entrée ou table de destination lors de l’exécution. Pour configurer ces entrées et sorties, utilisez la fonctionnalité des paramètres du service web afin de définir la propriété de *source de données* du module *Importer des données* ainsi que la propriété de destination des données du module *Exporter des données*.  Pour plus d’informations sur les paramètres du service web, consultez l [’entrée Paramètres du service web AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) du blog Cortana Intelligence et Machine Learning.

Pour configurer les paramètres du service web pour la requête d’importation et la table de destination :

1. Dans le volet des propriétés du module *Importer des données*, cliquez sur l’icône en haut à droite du champ **Requête de base de données**, puis sélectionnez **Définir en tant que paramètre du service web**.
2. Dans le volet des propriétés du module *Exporter des données*, cliquez sur l’icône en haut à droite du champ **Nom de la table de données**, puis sélectionnez **Définir en tant que paramètre du service web**.
3. En bas du volet des propriétés du module *Exporter des données* , dans la section **Paramètres de service web** , cliquez sur Requête de base de données puis renommez-la Requête.
4. Cliquez sur le champ **Nom de la table de données** et renommez-le **Table**.

Lorsque vous avez terminé, votre expérience devrait ressembler à l’image suivante :

![Aspect final de l’expérience.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Nous pouvez maintenant déployer l’expérience comme un service web.

## <a name="deploy-the-web-service"></a>Déploiement du service web
Vous pouvez déployer dans un service web classique ou nouveau.

### <a name="deploy-a-classic-web-service"></a>Déployer comme un service web classique
Pour déployer comme un service web classique et créer une application afin de l’utiliser :

1. En bas de la zone de dessin de l’expérience, cliquez sur Exécuter.
2. Une fois l’exécution terminée, cliquez sur **Déployer le service web** puis sélectionnez **Déployer le service web [classique]**.
3. Sur le tableau de bord du service web, recherchez votre clé API. Copiez et enregistrez cette clé pour une utilisation ultérieure.
4. Dans la table **Point de terminaison par défaut**, cliquez sur le **Exécution de lot** pour ouvrir la page d’aide de l’API.
5. Créez une application console en C# dans Visual Studio : **Nouveau** > **Projet** > **Visual C#** > **Bureau classique Windows** > **Application console (.NET Framework)**.
6. Sur la page d’aide de l’API, recherchez la section **Exemple de code** en bas de la page.
7. Copiez et collez l’exemple de code C# dans votre fichier Program.cs et supprimez toutes les références au stockage d’objets blob.
8. Mettez à jour la valeur de la variable *apiKey* avec la clé API enregistrée précédemment.
9. Recherchez la déclaration de requête et mettez à jour les valeurs des paramètres du service web passés aux modules *Importer des données* et *Exporter des données*. Dans le cas présent, vous utilisez la requête d’origine, mais définissez un nouveau nom de table.
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. Exécutez l'application. 

À la fin de l’exécution, une nouvelle table est ajoutée à la base de données contenant les résultats de notation.

### <a name="deploy-a-new-web-service"></a>Déployer comme un nouveau service web

> [!NOTE] 
> Pour déployer un nouveau service web, vous devez disposer d’autorisations suffisantes dans l’abonnement dans lequel déployer le service web. Pour en savoir plus, consultez [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md). 

Pour déployer comme un nouveau service web et créer une application afin de l’utiliser :

1. En bas de la zone de dessin de l’expérience, cliquez sur **Exécuter**.
2. ///MARDI 11H/// Une fois l’exécution terminée, cliquez sur **Déployer le service web** puis sélectionnez **Déployer le service web [nouveau]**.
3. Sur la page de l’expérience de déploiement, nommez votre service web, sélectionnez un plan de tarification, puis cliquez sur **Déployer**.
4. Sur la page **Quickstart**, cliquez sur **Consommer**.
5. Dans la section **Exemple de code**, cliquez sur **Lot**.
6. Créez une application console en C# dans Visual Studio : **Nouveau** > **Projet** > **Visual C#** > **Bureau classique Windows** > **Application console (.NET Framework)**.
7. Copiez et collez l’exemple de code C# dans votre fichier Program.cs.
8. Mettez à jour la valeur de la variable *apiKey* variable avec la **clé primaire** située dans la section des **informations de base sur la consommation**.
9. Recherchez la déclaration *scoreRequest* et mettez à jour les valeurs des paramètres du service Web passés aux modules *Importer des données* et *Exporter des données*. Dans le cas présent, vous utilisez la requête d’origine, mais définissez un nouveau nom de table.
   
        var scoreRequest = new
        {       
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. Exécutez l'application. 

