---
title: Nouveautés dans Azure Machine Learning | Microsoft Docs
description: De nouvelles fonctionnalités sont disponibles dans Microsoft Azure Learning.
services: machine-learning
documentationcenter: ''
author: serinakaye
ms.author: serinak
manager: mwinkle
editor: ''
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: bad36abc0960ec9b1bc8755a477f6fc7d2883384
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Nouveautés dans Azure Machine Learning

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>La mise à jour de mars 2017 de Microsoft Azure Machine Learning inclut la fonctionnalité suivante :



* Capacité dédiée pour les travaux du service d’exécution de lot d’Azure Machine Learning

    Le traitement par pool Batch de Machine Learning utilise le [service Azure Batch](../../batch/batch-technical-overview.md) pour fournir une échelle gérée par le client pour le service d’exécution de lot d’Azure Machine Learning. Le traitement par pool Batch vous permet de créer des pools Azure Batch, dans lesquels vous pouvez soumettre des travaux par lots, en vous assurant qu’ils s’exécutent de manière prévisible.

    Pour en savoir plus, voir [Service Azure Batch pour les travaux Machine Learning](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>La mise à jour d’août 2016 de Microsoft Azure Machine Learning inclut les fonctionnalités suivantes :
* Vous pouvez désormais gérer Les services web Machine via le nouveau portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/) qui fournit un emplacement pour gérer tous les aspects de votre service web.    
  * Fournit des [statistiques d’utilisation](manage-new-webservice.md) du service web.
  * Simplifie le test des appels de demande distante d’Azure Machine Learning à l’aide d’exemples de données.
  * Fournit une nouvelle page de test de service d’exécution de lot avec des exemples de données et un historique des travaux soumis.
  * Fournit une gestion de point de terminaison simplifiée.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>La mise à jour de juillet 2016 de Microsoft Azure Machine Learning inclut les fonctionnalités suivantes :
* Les services web sont désormais gérés en tant que ressources Azure via les interfaces [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) , ce qui permet les améliorations suivantes :
  * De nouvelles [API REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) pour déployer et gérer vos services web basés sur Resource Manager.
  * Un nouveau portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/) qui fournit un emplacement pour gérer tous les aspects de votre service web.
* Intègre un nouveau modèle de déploiement de service web multi-régions et basé sur un abonnement, qui utilise des API basées sur Resource Manager afin d’exploiter le fournisseur de ressources Resource Manager pour les services web.
* Présente de nouveaux [plans de tarification](https://azure.microsoft.com/pricing/details/machine-learning/) et des capacités de gestion en utilisant le nouveau fournisseur de ressources Resource Manager pour la facturation.
  * Vous pouvez à présent [déployer votre service web dans plusieurs régions](how-to-deploy-to-multiple-regions.md) sans avoir à créer un abonnement dans chaque région.
* Fournit des [statistiques d’utilisation](manage-new-webservice.md)du service web.
* Simplifie le test des appels de demande distante d’Azure Machine Learning à l’aide d’exemples de données.
* Fournit une nouvelle page de test de service d’exécution de lot avec des exemples de données et un historique des travaux soumis.

En outre, Machine Learning Studio a été mis à jour afin que vous puissiez déployer vers le nouveau modèle de service web ou continuer à déployer vers le modèle de service web classique. 

