---
title: Reformer un service web classique | Microsoft Docs
description: "Apprenez à reformer un modèle par programme et à mettre à jour le service Web pour utiliser le modèle reformé dans Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondlaghaeian
editor: 
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl
ms.openlocfilehash: a0ec3191448362f8bf98ca6b5133d173735046ed
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="retrain-a-classic-web-service"></a>Reformer un service web Classic
Le service web prédictif que vous avez déployé est le point de terminaison de notation par défaut. Les points de terminaison par défaut sont toujours synchronisés avec l’expérience originale d’apprentissage et de notation. Par conséquent, le modèle entraîné du point de terminaison par défaut ne peut pas être remplacé. Pour reformer le service web, vous devez ajouter un nouveau point de terminaison au service web. 

## <a name="prerequisites"></a>Prérequis
Vous devez avoir configuré une expérience de formation et une expérimentation prédictive comme indiqué dans [Reformer des modèles Machine Learning par programme](retrain-models-programmatically.md). 

> [!IMPORTANT]
> L’expérience prédictive doit être déployée comme un service web Machine Learning classique. 
> 
> 

Pour plus d’informations sur le déploiement de services web, consultez [Déployer un service web Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Ajouter un point de terminaison
Le service web prédictif que vous avez déployé contient un point de terminaison de notation par défaut qui est synchronisé avec le modèle formé pour les expériences de formation et de notation d’origine. Pour mettre à jour votre service web avec un nouveau modèle formé, vous devez créer un point de terminaison de notation. 

Pour créer un nouveau point de terminaison de notation, sur le service web prédictif pouvant être mis à jour avec le modèle entraîné :

> [!NOTE]
> Veillez à ajouter le point de terminaison au service web prédictif et non au service web d’apprentissage. Si vous avez correctement déployé à la fois un service web prédictif et un service web d’apprentissage, vous devez voir deux services web distincts répertoriés. Le service web prédictif doit se terminer par « [exp. prédictive] ».
> 
> 

Pour ajouter un point de terminaison à un service web, deux options s’offrent à vous :

1. Par programmation
2. Utilisation du portail de services web Microsoft Azure

### <a name="programmatically-add-an-endpoint"></a>Ajouter un point de terminaison par programmation
Vous pouvez ajouter des points de terminaison de notation à l’aide de l’exemple de code fourni dans ce [référentiel GitHub](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Utiliser le portail de services web Microsoft Azure pour ajouter un point de terminaison
1. Dans Machine Learning Studio, dans la colonne de navigation de gauche, cliquez sur Services web.
2. En bas du tableau de bord de services web, cliquez sur **Gérer les points de terminaison (préversion)**.
3. Cliquez sur **Add**.
4. Tapez un nom et une description pour le point de terminaison. Sélectionnez le niveau de journalisation et activez les exemples de données si nécessaire. Pour plus d’informations sur la journalisation, consultez [Activation de la journalisation pour les services web de Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Mettre à jour le modèle entraîné du point de terminaison ajouté
Pour terminer le processus de nouvel entraînement, vous devez mettre à jour le modèle entraîné du nouveau point de terminaison que vous avez ajouté.

Si vous avez ajouté le point de terminaison à l’aide de l’exemple de code, cela inclut l’emplacement de l’URL d’aide identifiée par la valeur *HelpLocationURL* dans la sortie.

Pour récupérer l’URL du chemin d’accès :

1. Copiez et collez l’URL dans votre navigateur.
2. Cliquez sur le lien Mettre à jour les ressources.
3. Copiez l’URL de la publication de la requête PATCH. Par exemple : 
   
     URL DU CORRECTIF : https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Vous pouvez maintenant utiliser le modèle entraîné pour mettre à jour le point de terminaison de notation que vous avez créé précédemment.

L’exemple de code suivant montre comment utiliser les éléments *BaseLocation*, *RelativeLocation*, *SasBlobToken* et l’URL d’application de correctifs pour mettre à jour le point de terminaison.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

L’*apiKey* et l’*endpointUrl* pour l’appel sont figurent sur le tableau de bord du point de terminaison.

La valeur du paramètre *Name* dans *Ressources* doit correspondre au nom de ressource du modèle formé enregistré dans l’expérience prédictive. Pour obtenir le nom de la ressource :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Machine Learning**.
3. Sous Nom, cliquez sur votre espace de travail, puis sur **Services web**.
4. Sous Nom, cliquez sur **Modèle de recensement [exp. prédictive]**.
5. Cliquez sur le nouveau point de terminaison que vous avez ajouté.
6. Dans le tableau de bord du point de terminaison, cliquez sur **Mettre à jour les ressources**.
7. Dans la page Documentation de l’API Mettre à jour la ressource du service web, vous trouverez le **Nom de la ressource** sous **Ressources actualisables**.

Si votre jeton SAP expire avant la fin de la mise à jour du point de terminaison, vous devez effectuer une opération GET avec l’ID du travail pour obtenir un nouveau jeton.

Lorsque le code a été exécuté avec succès, le nouveau point de terminaison doit commencer à utiliser le modèle de nouveau entraîné après environ 30 secondes.

## <a name="summary"></a>Résumé
À l’aide des API Retraining, vous pouvez mettre à jour le modèle entraîné d’un service web prédictif pour prendre en charge des scénarios tels que :

* Nouvel apprentissage périodique d’un modèle avec de nouvelles données.
* Distribution d’un modèle auprès des clients dans le but de leur permettre d’effectuer à nouveau l’apprentissage du modèle avec leurs propres données.

## <a name="next-steps"></a>étapes suivantes
[Résolution des problèmes de reformation d’un service web Azure Machine Learning classique](troubleshooting-retraining-models.md)

