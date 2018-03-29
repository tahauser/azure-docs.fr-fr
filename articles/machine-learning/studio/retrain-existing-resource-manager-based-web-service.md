---
title: Reformer un service web prédictif | Microsoft Docs
description: Apprenez à reformer un modèle et à mettre à jour le service web pour utiliser le modèle reformé dans Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: cc4c26a2-5672-4255-a767-cfd971e46775
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.openlocfilehash: 67d75a28ba65dbdc0a3a105f9e41a1c4f02f2615
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="retrain-an-existing-predictive-web-service"></a>Reformer un service web prédictif existant
Ce document décrit le processus de reformation pour le scénario suivant :

* Vous avez une expérience de formation et une expérience de prévision que vous avez déployées en tant que service web mis en œuvre.
* Vous disposez de nouvelles données et souhaitez que votre service web prédictif les utilise pour produire des scores.

> [!NOTE] 
> Pour déployer un nouveau service web, vous devez disposer d’autorisations suffisantes dans l’abonnement dans lequel déployer le service web. Pour en savoir plus, consultez la rubrique [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md). 

En partant de vos expériences et de votre service web existants, vous devez procédez comme suit :

1. Mettez le modèle à niveau.
   1. Modifiez votre expérience de formation de façon à autoriser les entrées et sorties du service web.
   2. Déployez l’expérience de formation en tant que service web de reformation.
   3. Utilisez le Service d’exécution de lots (BES, Batch Execution Service) de l’expérience de formation pour reformer le modèle.
2. Utilisez les applets de commande PowerShell d’Azure Machine Learning pour mettre à jour l’expérience prédictive.
   1. Connectez-vous à votre compte Azure Resource Manager.
   2. Obtenez la définition du service web.
   3. Exportez la définition du service web en tant que JSON.
   4. Mettez à jour la référence sur l’objet blob ilearner dans le JSON.
   5. Importez le JSON dans une définition de service web.
   6. Mettez à jour le service web avec la nouvelle définition de service web.

## <a name="deploy-the-training-experiment"></a>Déployer l’expérience de formation
Pour déployer l’expérience de formation en tant que service web de reformation, vous devez ajouter les entrées et sorties du service web au modèle. En connectant un module de *Sortie du service web* au module *[Former le modèle][train-model]* de l’expérience, vous permettez à l’expérience de formation de générer un nouveau modèle formé que vous pouvez utiliser dans votre expérience prédictive. Si vous avez un module *Évaluer le modèle*, vous pouvez également attacher la sortie du service web pour obtenir les résultats d’évaluation en tant que sortie.

Pour mettre à jour votre expérience de formation :

1. Connectez un module d’*Entrée du service web* à votre entrée de données (par exemple, un module *Nettoyer les données manquantes*). En règle générale, vous souhaitez vous assurer que vos données d’entrée sont traitées de la même manière que vos données de formation d’origine.
2. Connectez un module de *Sortie du service web* à la sortie de votre module *Former le modèle*.
3. Si vous avez un module *Évaluer le modèle* et souhaitez sortir les résultats d’évaluation, connectez un module de *Sortie du service web* à la sortie de votre module *Évaluer le modèle*.

Exécutez votre expérience.

Ensuite, vous devez déployer l’expérience de formation en tant que service web qui produit un modèle formé et les résultats d’évaluation du modèle.  

En bas du canevas de l’expérience, cliquez sur **Configurer le service web**, puis sélectionnez **Déployer le service web [nouveau]**. Le portail des services web Azure Machine Learning s’ouvre sur la page **Déployer le service web**. Tapez un nom pour votre service web, choisissez un plan de paiement, puis cliquez sur **Déployer**. Vous pouvez utiliser la méthode Exécution par lot uniquement pour créer des modèles formés.

## <a name="retrain-the-model-with-new-data-by-using-bes"></a>Reformer le modèle avec les nouvelles données en utilisant le BES
Pour cet exemple, nous utilisons le langage C# pour créer l’application de reformation. Pour accomplir cette tâche, vous pouvez également utiliser un code Python ou R.

Pour appeler les API de reformation :

1. Créez une application console en C# dans Visual Studio : **Nouveau** > **Projet** > **Visual C#** > **Bureau classique Windows** > **Application console (.NET Framework)**.
2. Connectez-vous au portail des services web Azure Machine Learning.
3. Cliquez sur le service web que vous utilisez.
4. Cliquez sur **Consommer**.
5. En bas de la page **Utiliser**, dans la section **Exemple de code**, cliquez sur **Lot**.
6. Copiez l’exemple de code C# pour l’exécution par lot et collez-le dans le fichier Program.cs. Assurez-vous que l’espace de noms reste intact.

Ajoutez le package NuGet Microsoft.AspNet.WebApi.Client comme indiqué dans les commentaires. Pour ajouter la référence à Microsoft.WindowsAzure.Storage.dll, il se peut que vous deviez au préalable installer la [bibliothèque cliente pour les services de Stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

La capture d’écran suivante montre la page **Consommer** du portail des services web Azure Machine Learning.

![Page Consommer][1]

### <a name="update-the-apikey-declaration"></a>Mettre à jour la déclaration apiKey
Localisez la déclaration **apikey**:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Dans la section des **informations de base sur la consommation** de la page **Utiliser**, recherchez la clé primaire et copiez-la dans la déclaration **apiKey**.

### <a name="update-the-azure-storage-information"></a>Mettre à jour les informations Azure Storage
L’exemple de code BES charge un fichier à partir d’un lecteur local (par exemple, « C:\temp\CensusInput.csv ») vers le Stockage Azure, le traite, réécrit les résultats dans le Stockage Azure.  

Après que vous avez exécuté cette procédure, le flux de travail obtenu doit être semblable à ce qui suit :

![Flux de travail obtenu après l’exécution][4]

1. Connectez-vous au portail Azure.
2. Dans la colonne de navigation de gauche, cliquez sur **Autres services**, recherchez **Comptes de stockage** et sélectionnez-le.
3. Dans la liste des comptes de stockage, sélectionnez-en un pour stocker le modèle reformé.
4. Dans la colonne de navigation de gauche, cliquez sur **Clés d’accès**.
5. Copiez et enregistrez la **Clé d’accès primaire**.
6. Dans la colonne de navigation de gauche, cliquez sur **Conteneurs**.
7. Sélectionnez un conteneur existant ou créez-en un et enregistrez le nom.

Localisez les déclarations *StorageAccountName*, *StorageAccountKey* et *StorageContainerName*, puis mettez à jour les valeurs que vous avez enregistrées à partir du portail.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Vous devez également vous assurer que le fichier d’entrée est disponible à l’emplacement spécifié dans le code.

### <a name="specify-the-output-location"></a>Spécifier l’emplacement de sortie
Lorsque vous spécifiez l’emplacement de sortie dans la Charge utile des demandes, l’extension du fichier spécifiée dans *RelativeLocation* doit être spécifiée en tant que valeur `ilearner`. Voir l’exemple suivant :

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Voici un exemple de sortie de reformation :

![Sortie du nouvel apprentissage.][6]

## <a name="evaluate-the-retraining-results"></a>Évaluer les résultats de la reformation
Lorsque vous exécutez l’application, la sortie inclut l’URL et le jeton de signature d’accès partagé (SAP) nécessaires pour accéder aux résultats de l’évaluation.

Vous pouvez consulter les résultats des performances du modèle reformé en combinant les éléments *BaseLocation*, *RelativeLocation* et *SasBlobToken* dans les résultats de sortie de *output2* (comme le montre l’image de sortie de reformation précédente), puis en collant l’URL complète dans la barre d’adresses du navigateur.  

Examinez les résultats pour déterminer si le modèle de nouveau entraîné est suffisamment performant pour remplacer le modèle existant.

Copiez les éléments *BaseLocation*, *RelativeLocation* et *SasBlobToken* des résultats de sortie.

## <a name="retrain-the-web-service"></a>Reformer le service web
Lorsque vous reformez un nouveau service web, vous mettez à jour la définition de service web prédictif pour référencer le nouveau modèle formé. La définition du service web est une représentation interne du modèle formé du service web, qui n’est pas directement modifiable. Vérifiez que vous récupérez la définition du service web pour votre expérience prédictive et non pour votre expérience de formation.

## <a name="sign-in-to-azure-resource-manager"></a>Se connecter à Azure Resource Manager
Vous devez tout d’abord vous connecter à votre compte Azure à partir de l’environnement PowerShell à l’aide de l’applet de commande [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx).

## <a name="get-the-web-service-definition-object"></a>Obtenir l’objet Définition du service web
Ensuite, obtenez l’objet Définition du service web en appelant l’applet de commande [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx).

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Pour déterminer le nom du groupe de ressources d’un service web existant, exécutez l’applet de commande Get-AzureRmMlWebService sans paramètres pour afficher les services web dans votre abonnement. Recherchez le service web et examinez son ID de service web. Le nom du groupe de ressources est le quatrième élément de l’ID, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom du groupe de ressources est Default-MachineLearning-SouthCentralUS.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Pour déterminer le nom du groupe de ressources d’un service web existant, vous pouvez également vous connecter au portail des services web Azure Machine Learning. Sélectionnez le service web. Le nom de groupe de ressources est le cinquième élément de l’URL du service web, juste après l’élément *resourceGroups* . Dans l’exemple suivant, le nom du groupe de ressources est Default-MachineLearning-SouthCentralUS.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exporter l’objet Définition du service web en tant que JSON
Pour modifier la définition du modèle formé de manière à utiliser le modèle nouvellement formé, vous devez d’abord utiliser l’applet de commande [Export-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx) pour l’exporter vers un fichier au format JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Mettre à jour la référence à l’objet blob ilearner
Dans les ressources, recherchez le [modèle formé], mettez à jour la valeur *uri* dans le nœud *locationInfo* avec l’URI de l’objet blob ilearner. L’URI est générée en combinant les valeurs *BaseLocation* et *RelativeLocation* de la sortie de l’appel de reformation BES.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importer le JSON dans un objet Définition du service web
Vous devez utiliser l’applet de commande [Import-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) pour convertir le fichier JSON modifié en un objet Définition du service web que vous pouvez utiliser pour mettre à jour l’expérience prédictive.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Mise à jour du service web
Enfin, utilisez l’applet de commande [Update-AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) pour mettre à jour l’expérience prédictive.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

[1]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
