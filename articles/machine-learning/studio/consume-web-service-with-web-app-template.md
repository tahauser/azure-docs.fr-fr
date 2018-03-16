---
title: "Utiliser un service web Machine Learning à l’aide d’un modèle d’application web | Microsoft Docs"
description: "Utilisez un modèle d’application Web dans Azure Marketplace pour exploiter un service Web prédictif dans Azure Machine Learning."
keywords: "service Web, opérationnalisation, API REST, apprentissage automatique"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: raymondl
ms.openlocfilehash: f7efa647fa6afc247509cd4a52066c0459f75ca3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="consume-an-azure-machine-learning-web-service-by-using-a-web-app-template"></a>Utiliser un service web Azure Machine Learning à l’aide d’un modèle d’application web

Vous pouvez développer un modèle prédictif et le déployer en tant que service web Azure avec :
- Azure Machine Learning Studio
- Des langages du type R ou Python 

Après cela, vous pouvez accéder au modèle opérationnalisé à l’aide d’une API REST.

Il existe plusieurs moyens d’utiliser l’API REST et d’accéder au service Web. Vous pouvez par exemple écrire une application en C#, R ou Python en utilisant l’exemple de code généré lors du déploiement du service web. Cet exemple de code est disponible sur le [portail des services web Machine Learning](https://services.azureml.net/quickstart) et dans le tableau de bord du service web, dans Machine Learning Studio. Vous pouvez également utiliser l’exemple de classeur Microsoft Excel créé en même temps.

Toutefois, le moyen le plus rapide et le plus simple d’accéder à votre service web est d’utiliser les modèles d’applications web de la [Place de marché Microsoft Azure](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-machine-learning-web-app-templates"></a>Modèles d’applications web Azure Machine Learning
Les modèles d’applications Web disponibles dans Azure Marketplace peuvent générer une application Web personnalisée qui connaît les données d’entrée et les résultats attendus de votre service Web. Il vous suffit de donner à l’application Web l’accès à votre service Web et aux données associées, et le modèle fait le reste.

Il existe deux modèles :

* [Modèle d’application Web Azure ML Request-Response Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Modèle d’application Web Azure ML Batch Execution Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Chaque modèle crée un exemple d’application ASP.NET en utilisant l’URI et la clé de l’API correspondant à votre service web. Le modèle déploie ensuite l’application en tant que site web sur Azure. 

Le modèle RRS (Request-Response Service) crée une application web qui vous permet d’envoyer une seule ligne de données au service web afin d’obtenir un résultat unique. Le modèle BES (Batch Execution Service) crée une application web qui vous permet d’envoyer un grand nombre de lignes de données de manière à obtenir plusieurs résultats.

Aucun code n’est nécessaire pour utiliser ces modèles. Vous devez simplement spécifier la clé API et l’URI pour permettre au modèle de générer automatiquement l’application.

Pour obtenir la clé API et l’URI de requête d’un service web :

1. Dans le [portail Services Web](https://services.azureml.net/quickstart), sélectionnez **Web Services** (Services web) en haut de la page. Sinon, pour un service web classique, sélectionnez **Classic Web Services** (Services web classiques).
2. Sélectionnez le service web auquel vous souhaitez accéder.
3. Pour un service web classique, sélectionnez le point de terminaison auquel vous souhaitez accéder.
4. Sélectionnez **Consume** (Utiliser) en haut de la page.
5. Copiez la clé primaire ou secondaire, puis enregistrez-la.
6. Si vous créez un modèle RRS, copiez l’URI **Request-Response** (Requête-réponse), puis enregistrez-le. Si vous créez un modèle BES, copiez l’URI **Batch Requests** (Requêtes de lots), puis enregistrez-le.


## <a name="how-to-use-the-request-response-service-template"></a>Comment utiliser le modèle RRS
Procédez comme suit pour utiliser le modèle d’application web RRS (voir schéma ci-dessous).

![Procédure d’utilisation du modèle Web RSS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Nouveau**, puis sélectionnez **Azure ML Request-Response Service Web App** et **Créer**. 
3. Dans le volet **Créer** :
   
   * Donnez un nom unique à votre application Web. L’URL de l’application web sera constituée de ce nom, suivi de **.azurewebsites.net**. Par exemple : **http://carprediction.azurewebsites.net**.
   * Sélectionnez l’abonnement Azure et les services sous lesquels est exécuté votre service Web.
   * Sélectionnez **Créer**.
     
   ![Créer une application web][image5]

4. Une fois le déploiement de l’application web terminé, sélectionnez **l’URL** dans la page des paramètres de l’application web d’Azure, ou entrez l’URL dans un navigateur web. Entrez, par exemple : **http://carprediction.azurewebsites.net**.
5. À la première exécution de l’application web, vous êtes invité à fournir l’URL de publication de l’API sous **API Post URL**, ainsi que la clé API sous **API Key**. Entrez les valeurs que vous avez enregistrées précédemment (l’URI de la requête et la clé API, respectivement). Sélectionnez **Envoyer**.
     
   ![Entrer l’URI de publication et la clé API][image6]

6. L’application web affiche la page **Configuration de l’application web** avec les paramètres du service web actif. Vous pouvez ici modifier les paramètres utilisés par l’application web.
   
   > [!NOTE]
   > Une modification des paramètres à ce stade n’affecte que l’application Web concernée. Les paramètres par défaut de votre service Web ne seront pas modifiés. Par exemple, si vous modifiez ici le texte de la **Description**, l’opération n’aura aucun effet sur la description affichée sur le tableau de bord du service web dans Machine Learning Studio.
   > 
   > 
   
    Quand vous avez terminé, sélectionnez **Enregistrer les modifications**, puis sélectionnez **Atteindre la page de démarrage**.

7. Vous pouvez entrer les valeurs à envoyer à votre service web dans la page d’accueil. Sélectionnez **Envoyer** lorsque vous avez terminé. Le résultat est alors renvoyé.

Si vous souhaitez revenir à la page **Configuration**, accédez à la page **setting.aspx** de l’application web. Par exemple, accédez à **http://carprediction.azurewebsites.net/setting.aspx**. Vous êtes invité à entrer de nouveau la clé API. Vous devez accéder à cette page pour mettre à jour les paramètres.

Vous pouvez arrêter, redémarrer ou supprimer l’application web dans le portail Azure comme n’importe quelle autre application web. Tant qu’elle est en cours d’exécution, vous pouvez accéder à l’adresse web de base et entrer les nouvelles valeurs.

## <a name="how-to-use-the-batch-execution-service-template"></a>Comment utiliser le modèle BES
Vous pouvez utiliser le modèle d’application web BES de la même façon que le modèle RRS. La seule différence est qu’il permet d’utiliser l’application web créée pour envoyer plusieurs lignes de données et recevoir plusieurs résultats.

Les valeurs d’entrée d’un service web d’exécution de lot peuvent provenir du stockage Azure ou d’un fichier local. Les résultats sont stockés dans un conteneur de stockage Azure. Par conséquent, vous avez besoin d’un conteneur de stockage Azure pour stocker les résultats retournés par l’application web. Vous devez également préparer vos données d’entrée.

![Procédure d’utilisation du modèle Web BES][image2]

1. Pour créer l’application web BES, suivez la même procédure que celle utilisée pour le modèle RRS. Toutefois, dans ce cas, vous devez accéder à [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) pour ouvrir le modèle BES dans la Place de marché Microsoft Azure. Sélectionnez **Créer une application web**.

2. Pour spécifier l’emplacement de stockage des résultats, indiquez les informations du conteneur de destination dans la page d’accueil de l’application web. Indiquez également l’emplacement à partir duquel l’application web peut obtenir les valeurs d’entrée, à savoir dans un fichier local ou dans un conteneur de stockage Azure.
   Sélectionnez **Envoyer**.
   
   ![Informations sur le stockage][image7]

L’application web affiche une page avec l’état de la tâche. Une fois la tâche terminée, vous obtenez l’emplacement des résultats dans le Stockage Blob Azure. Vous avez également la possibilité de télécharger les résultats dans un fichier local.

## <a name="for-more-information"></a>Pour plus d’informations
Pour en savoir plus sur :

* La création d’une expérience d’apprentissage automatique avec Machine Learning Studio, consultez [Création de votre première expérience dans Azure Machine Learning Studio](create-experiment.md)
* Le déploiement de votre expérience d’apprentissage automatique sous la forme d’un service web, consultez [Déploiement d’un service web Azure Machine Learning](publish-a-machine-learning-web-service.md)
* D’autres manières d’accéder à votre service web, consultez [Utilisation d’un service web Azure Machine Learning](consume-web-services.md)

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
