---
title: Résoudre les problèmes de reformation d’un service web Azure Machine Learning Classic | Microsoft Docs
description: Identifiez et corrigez les problèmes courants rencontrés lorsque vous reformez le modèle d’un service web Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: garyericson
manager: hjerez
editor: ''
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 011/01/2017
ms.author: garye
ms.openlocfilehash: 486f66e3d864a172ba301d017c12406ebafc4824
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Résolution des problèmes de reformation d’un service web Machine Learning classique
## <a name="retraining-overview"></a>Vue d’ensemble de la reformation
Lorsque vous déployez une expérience prédictive en tant que service web d’évaluation, il s’agit d’un modèle statique. Lorsque de nouvelles données sont disponibles ou lorsque le consommateur de l’API a ses propres données, le modèle doit être reformé. 

Pour une procédure pas à pas complète du processus de reformation d’un service web classique, consultez [Reformation des modèles Machine Learning par programme](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Processus de reformation
Lorsque vous devez reformer le service web, vous devez ajouter quelques éléments supplémentaires :

* Un service web déployé à partir de l’expérience de formation. L’expérience doit avoir un module de **sortie de service web** attaché à la sortie du module **Modèle de formation**.  
  
    ![Attachez la sortie de service web au modèle de formation.][image1]
* Un nouveau point de terminaison ajouté à votre service web d’évaluation.  Vous pouvez ajouter le point de terminaison par programme à l’aide de l’exemple de code référencé dans la rubrique Reformation des modèles Machine Learning par programme ou via le portail des services web Azure Machine Learning.

Vous pouvez utiliser l’exemple de code C# de la page d’aide API du service web de formation pour reformer le modèle. Après avoir évalué les résultats et que vous en êtes satisfait, vous mettez à jour le service web d’évaluation du modèle formé à l’aide du nouveau point de terminaison que vous avez ajouté.

Une fois tous les éléments en place, les principales étapes à suivre pour reformer le modèle sont les suivantes :

1. Appelez le service web de formation : l’appel est destiné au service d’exécution de lots (BES, Batch Execution Service), et non au service de requête-réponse (RRS, Request-Response Service). Vous pouvez utiliser l’exemple de code C# sur la page d’aide API pour effectuer l’appel. 
2. Recherchez les valeurs pour *BaseLocation*, *RelativeLocation* et *SasBlobToken* : ces valeurs sont retournées dans la sortie à partir de votre appel au service web de formation. 
   ![Affichage de la sortie de l’exemple de reformation et des valeurs BaseLocation, RelativeLocation et SasBlobToken.][image6]
3. Mettez à jour le point de terminaison ajouté à partir du service web d’évaluation avec le nouveau modèle formé : à l’aide de l’exemple de code fourni dans la rubrique Reformation des modèles Machine Learning par programme, mettez à jour le nouveau point de terminaison que vous avez ajouté au modèle d’évaluation avec le modèle nouvellement formé à partir du service web de formation.

## <a name="common-obstacles"></a>Obstacles courants
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Vérifiez que vous disposez de l’URL d’application de correctifs appropriée
L’URL d’application de correctifs que vous utilisez doit être celle associée au nouveau point de terminaison d’évaluation que vous avez ajouté au service web d’évaluation. Il existe plusieurs façons d’obtenir l’URL des CORRECTIFS :

**Option 1 : par programme**

Pour obtenir l’URL d’application de correctifs appropriée :

1. Exécutez l’exemple de code [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) .
2. À partir de la sortie d’AddEndpoint, recherchez la valeur *HelpLocation* et copiez l’URL.
   
   ![HelpLocation dans la sortie de l’exemple addEndpoint.][image2]
3. Collez l’URL dans un navigateur pour accéder à une page qui fournit des liens d’aide pour le service web.
4. Cliquez sur le lien de **Mettre à jour la ressource** pour ouvrir la page d’aide sur le correctif.

**Option 2 : utiliser le portail des services web Azure Machine Learning**

1. Connectez-vous au portail des [services web Azure Machine Learning](https://services.azureml.net/).
2. Cliquez sur **Services web** ou **Services web classiques** en haut de la page.
4. Cliquez sur le service web d’évaluation que vous utilisez (si vous n’avez pas modifié le nom par défaut du service web, il doit se terminer par « [Scoring Exp.] »).
5. Cliquez sur **+NOUVEAU**.
6. Après l’ajout du point de terminaison, cliquez sur le nom du point de terminaison.
7. Sous **l’URL d’application de correctifs**, cliquez sur **Aide de l’API** pour ouvrir la page d’aide d’application de correctifs.

> [!NOTE]
> Si vous avez ajouté le point de terminaison au service web de formation plutôt qu’au service web prédictif, lorsque vous cliquez sur le lien **Mettre à jour la ressource**, vous recevrez un message d’erreur signalant que cette fonctionnalité n’est pas prise en charge ou disponible dans ce contexte. Ce service web n’a aucune ressource actualisable. Veuillez nous excuser pour ce désagrément. Nous travaillons actuellement à l’amélioration de ce flux de travail.
> 
> 

La page d’aide d’application de correctifs contient l’URL d’application de correctifs que vous devez utiliser et fournit un exemple de code que vous pouvez utiliser pour l’appeler.

![URL d’application de correctifs.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Vérifiez que vous mettez à jour le point de terminaison d’évaluation approprié
* N’appliquez pas de correctifs au service web de formation : l’opération d’application de correctifs doit être effectuée sur le service web d’évaluation.
* N’appliquez pas de correctifs au point de terminaison par défaut du service web : l’opération d’application de correctifs doit être effectuée sur le nouveau point de terminaison du service web d’évaluation que vous avez ajouté.

Vous pouvez vérifier sur quel service web se trouve le point de terminaison en visitant le portail des services web. 

> [!NOTE]
> Veillez à ajouter le point de terminaison au service web prédictif et non au service web d’apprentissage. Si vous avez correctement déployé à la fois un service web prédictif et un service web d’apprentissage, vous devez voir deux services web distincts répertoriés. Le service web prédictif doit se terminer par « [exp. prédictive] ».
> 
> 

1. Connectez-vous au portail des [services web Azure Machine Learning](https://services.azureml.net/).
2. Cliquez sur **Services web** ou **Services web classiques**.
3. Sélectionnez votre service web prédictif.
4. Vérifiez que votre nouveau point de terminaison a été ajouté au service web.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Vérifier que votre espace de travail se trouve dans la même région que le service web
1. Connectez-vous à [Machine Learning Studio](https://studio.azureml.net/).
2. En haut de la page, cliquez sur la liste déroulante de vos espaces de travail.

   ![Interface utilisateur de la région Machine Learning.][image4]

3. Vérifiez la région dans laquelle se trouve votre espace de travail.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
