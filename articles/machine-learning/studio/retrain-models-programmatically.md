---
title: "Reformation des modèles Machine Learning par programme | Microsoft Docs"
description: "Apprenez à reformer un modèle par programme et à mettre à jour le service Web pour utiliser le modèle reformé dans Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl;garye
ms.openlocfilehash: d228021564cdfe5c898c67cce0038b3ec36d014b
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="retrain-machine-learning-models-programmatically"></a>Reformation des modèles Machine Learning par programme
Cette procédure pas à pas explique comment reformer par programmation un service web Azure Machine Learning en utilisant C# et le service d’exécution de lot Machine Learning.

Une fois le modèle reformé, les procédures pas à pas suivantes montrent comment le mettre à jour dans votre service web prédictif :

* Si vous avez déployé un service web classique dans le portail des services web Azure Machine Learning, consultez [Reformer un service web classique](retrain-a-classic-web-service.md). 
* Si vous avez déployé un nouveau service web, consultez [Reformer un nouveau service web à l’aide des applets de commande de gestion Machine Learning](retrain-new-web-service-using-powershell.md).

Pour une présentation du processus de reformation, voir [Reformer un modèle Machine Learning](retrain-machine-learning-model.md).

Si vous voulez commencer avec votre service web Azure Resource Manager existant, consultez [Reformer un service web prédictif existant](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Créez une expérience d'apprentissage
Pour cet exemple, vous allez utiliser « Sample 5 : Train, Test, Evaluate for Binary Classification : Adult Dataset » dans les exemples Microsoft Azure Machine Learning. 

Pour créer l’expérience :

1. Connectez-vous à Microsoft Azure Machine Learning Studio. 
2. En bas à droite du tableau de bord, cliquez sur **Nouveau**.
3. Parmi les exemples Microsoft, sélectionnez l’exemple 5.
4. Pour renommer l’expérience, en haut du canevas de l’expérience, sélectionnez le nom de l’expérience « Sample 5 : Train, Test, Evaluate for Binary Classification : Adult Dataset ».
5. Tapez Modèle de recensement.
6. En bas de la zone de dessin de l’expérience, cliquez sur **Exécuter**.
7. Cliquez sur **Configurer le service web**, puis sélectionnez **Reformation du service web**. 

L’exemple suivant illustre l’expérience initiale.
   
   ![Expérience initiale.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Créer une expérience prédictive et la publier comme service web
Ensuite, vous créez une expérience prédictive.

1. En bas du canevas de l’expérience, cliquez sur **Configurer le service web**, puis sélectionnez **Service web prédictif**. Le modèle est enregistré sous forme d’un modèle formé, et des modules d’entrée et de sortie du service web sont ajoutés. 
2. Cliquez sur **Exécuter**. 
3. Une fois l’exécution de l’expérience terminée, cliquez sur **Déployer le service web [classique]** ou **Déployer le service web [nouveau]**.

> [!NOTE] 
> Pour déployer un nouveau service web, vous devez disposer d’autorisations suffisantes dans l’abonnement dans lequel déployer le service web. Pour en savoir plus, consultez la rubrique [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Déployer l’expérience de formation comme service web de formation
Pour reformer le modèle, vous devez déployer l’expérience de formation que vous avez créée comme service web de reformation. Ce service web a besoin d’un module *Sortie du service web* connecté au module *[Former le modèle][train-model]* afin de pouvoir produire de nouveaux modèles formés.

1. Pour revenir à l’expérience d’apprentissage, cliquez sur l’icône Expériences dans le volet gauche, puis sur l’expérience nommée Modèle de recensement.  
2. Dans la zone de recherche des éléments d’expérience, tapez Service web. 
3. Faites glisser un module *Entrée du service web* dans le canevas de l’expérience et connectez sa sortie au module *Nettoyer les données manquantes*.  Cela garantit que vos données de reformation sont traitées de la même manière que vos données de formation d’origine.
4. Faites glisser deux modules *Sortie du service web* sur le canevas de l’expérience. Connectez la sortie du module *Former le modèle* à l’un des modules, et la sortie du module *Évaluer le modèle* à l’autre. La sortie du service web pour **Former le modèle** nous fournit le nouveau modèle formé. La sortie attachée au module **Évaluer le modèle** retourne la sortie de celui-ci, c’est-à-dire les résultats des performances.
5. Cliquez sur **Exécuter**. 

Ensuite, vous devez déployer l’expérience de formation comme service web qui produit un modèle formé et les résultats d’évaluation du modèle. La procédure diffère selon que vous utilisez un service web classique ou un nouveau service web.  

**Service web classique**

En bas du canevas de l’expérience, cliquez sur **Configurer le service web**, puis sélectionnez **Service web [classique]**. Le **Tableau de bord** du service web s’affiche avec la clé API et la page d’aide d’API pour l’exécution par lots. Seule la méthode d’exécution par lots peut être utilisée pour créer des modèles entraînés.

**Nouveau service web**

En bas du canevas de l’expérience, cliquez sur **Configurer le service web**, puis sélectionnez **Déployer le service web [nouveau]**. Le portail de services web Azure Machine Learning s’ouvre sur la page Déployer le service web. Tapez un nom pour votre service web, choisissez un plan de paiement, puis cliquez sur **Déployer**. Seule la méthode d’exécution par lot peut être utilisée pour créer des modèles formés.

Dans les deux cas, une fois l’exécution de l’expérience terminée, le flux de travail doit ressembler à ce qui suit :

![Flux de travail produit après l’exécution.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Effectuer à nouveau l’apprentissage du modèle avec de nouvelles données à l’aide de BES
Pour cet exemple, vous utilisez le langage C# pour créer l’application de reformation. Pour accomplir cette tâche, vous pouvez également utiliser l’exemple de code Python ou R.

Pour appeler les API Retraining :

1. Créez une application console en C# dans Visual Studio : **Nouveau** > **Projet** > **Visual C#** > **Bureau classique Windows** > **Application console (.NET Framework)**.
2. Connectez-vous au portail de services web Azure Machine Learning.
3. Si vous utilisez un service web classique, cliquez sur **Services web classiques**.
   1. Cliquez sur le service web utilisé.
   2. Cliquez sur le point de terminaison par défaut.
   3. Cliquez sur **Consommer**.
   4. En bas de la page **Utiliser**, dans la section **Exemple de code**, cliquez sur **Lot**.
   5. Passez à l’étape 5 de cette procédure.
4. Si vous utilisez un nouveau service web, cliquez sur **Services web**.
   1. Cliquez sur le service web utilisé.
   2. Cliquez sur **Consommer**.
   3. En bas de la page Utiliser, dans la section **Exemple de code**, cliquez sur **Lot**.
5. Copiez l’exemple de code C# pour l’exécution par lot et collez-le dans le fichier Program.cs en veillant à ne pas modifier l’espace de noms.

Ajoutez le package NuGet Microsoft.AspNet.WebApi.Client comme indiqué dans les commentaires. Pour ajouter une référence à Microsoft.WindowsAzure.Storage.dll, il se peut que vous deviez au préalable installer la bibliothèque cliente pour les services de stockage Microsoft Azure. Pour plus d’informations, consultez [cette page](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Mettre à jour la déclaration apiKey
Localisez la déclaration **apiKey** .

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Dans la section des **informations de base sur la consommation** de la page **Utiliser**, recherchez la clé primaire et copiez-la dans la déclaration **apiKey**.

### <a name="update-the-azure-storage-information"></a>Mettre à jour les informations Azure Storage
L’exemple de code BES charge un fichier à partir d’un lecteur local (par exemple, « C:\temp\CensusInput.csv ») vers Azure Storage, le traite et réécrit les résultats dans Azure Storage.  

Pour réaliser cette tâche, vous devez récupérer les informations de nom de compte, de clé et de conteneur de stockage pour votre compte de stockage depuis le portail Azure Classic et mettre à jour les valeurs correspondantes dans le code. 

1. Connectez-vous au portail Azure Classic.
2. Dans la colonne de navigation de gauche, cliquez sur **Stockage**.
3. Dans la liste des comptes de stockage, sélectionnez-en un pour stocker le modèle reformé.
4. En bas de la page, cliquez sur **Gérer les clés d’accès**.
5. Copiez et enregistrez la **clé d’accès primaire** , puis fermez la boîte de dialogue. 
6. En haut de la page, cliquez sur **Conteneurs**.
7. Sélectionnez un conteneur existant ou créez-en un et enregistrez le nom.

Localisez les déclarations *StorageAccountName*, *StorageAccountKey* et *StorageContainerName*, et mettez à jour les valeurs que vous avez enregistrées à partir du portail Azure.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Vous devez également vous assurer que le fichier d’entrée est disponible à l’emplacement spécifié dans le code. 

### <a name="specify-the-output-location"></a>Spécifier l’emplacement de sortie
Lorsque vous spécifiez l’emplacement de sortie dans la charge utile des demandes, l’extension du fichier spécifiée dans *RelativeLocation* doit avoir pour valeur ilearner. 

Voir l’exemple suivant :

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> Le nom de vos emplacements de sortie peut être différent de ceux de cette procédure pas à pas, selon l’ordre dans lequel vous avez ajouté les modules de sortie du service web. Étant donné que vous avez défini cette expérience de formation avec deux sorties, les résultats incluent les informations d’emplacement de stockage des deux.  
> 
> 

![Sortie du nouvel apprentissage.][6]

Diagramme 4 : Sortie du nouvel apprentissage.

## <a name="evaluate-the-retraining-results"></a>Évaluer les résultats du nouvel apprentissage
Lorsque vous exécutez l’application, la sortie inclut le jeton SAP et l’URL nécessaires pour accéder aux résultats de l’évaluation.

Vous pouvez consulter les résultats des performances du modèle de nouveau entraîné en combinant *BaseLocation*, *RelativeLocation* et *SasBlobToken* dans les résultats de sortie de *output2* (comme le montre l’image de sortie de la reformation précédente), puis en collant l’URL complète dans la barre d’adresses du navigateur.  

Examinez les résultats pour déterminer si le modèle de nouveau entraîné est suffisamment performant pour remplacer le modèle existant.

Copiez les éléments *BaseLocation*, *RelativeLocation* et *SasBlobToken* des résultats de sortie. Vous allez les utiliser pendant le processus de reformation.

## <a name="next-steps"></a>Étapes suivantes
Si vous avez déployé le service web prédictif en cliquant sur **Déployer un service web [classique]**, consultez [Reformer un service web classique](retrain-a-classic-web-service.md).

Si vous avez déployé le service web prédictif en cliquant sur **Déployer un service web [nouveau]**, consultez [Reformer un nouveau service web à l’aide des applets de commande de gestion Machine Learning](retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
