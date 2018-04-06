---
title: Tarification et facturation des services Azure Machine Learning
description: Cet article contient des questions fréquemment posées sur la tarification et la facturation des fonctionnalités en préversion de Azure Machine Learning
services: machine-learning
author: j-martens
ms.author: jmartens
manager: cgronlund
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 5e057f3fe4a4ff06e0acac29b3dcd11fa901fc40
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="pricing-and-billing-for-azure-machine-learning-services"></a>Tarification et facturation des services Azure Machine Learning

Pour plus d’informations ou pour afficher un exemple de facture, visitez la [page de tarification Azure](https://azure.microsoft.com/pricing/details/machine-learning-services/).  

Voici quelques questions fréquemment posées concernant la facturation et la tarification.

## <a name="can-i-use-azure-machine-learning-for-free-during-preview"></a>Puis-je utiliser Azure Machine Learning gratuitement pendant la période de préversion ?    

L’application Azure Machine Learning Workbench est disponible gratuitement pour les abonnés Azure.

Le Service d’expérimentation et la Gestion des modèles offrent des niveaux gratuits, en plus des niveaux payants disponibles à prix réduit pendant la période de Préversion publique.

## <a name="am-i-charged-based-on-how-many-experiments-i-run"></a>Suis-je facturé sur la base du nombre d’expériences menées ?

Non. Le Service d’expérimentation vous autorise à faire autant d’expériences que nécessaire, et facture uniquement sur la base du nombre d’utilisateurs. Les ressources de calcul liées à l’expérimentation sont facturées séparément.  Nous vous encourageons à effectuer plusieurs expériences avant d’obtenir le modèle optimal pour votre solution. 

## <a name="am-i-charged-based-on-how-many-times-my-web-services-is-called"></a>Suis-je facturé en fonction du nombre d’appels à mes services web ?

Non. Les services web peuvent être appelés aussi souvent que nécessaire, sans que cela affecte la facturation de la Gestion des modèles. Vous contrôlez totalement l’échelle de vos déploiements en fonction des besoins de vos applications.

## <a name="how-can-i-scale-the-units-i-purchase-in-the-azure-machine-learning-model-management"></a>Comment mettre à l’échelle le nombre d’unités que j’achète dans Gestion des modèles Machine Learning ?

Vous pouvez augmenter ou réduire le nombre d’unités via le portail Azure ou l’interface de ligne de commande. 

## <a name="what-does-a-bill-look-like"></a>À quoi ressemble une facture ?

Les factures sont calculées quotidiennement. Pour la facturation, un jour commence à minuit UTC. Les factures sont générées mensuellement. Les frais liés à la consommation des services Azure conjointement avec Azure Machine Learning font l’objet d’une facturation distincte. Il s’agit notamment des frais suivants : 
- Frais de calcul
- HDInsight
- Azure Container Service
- Azure Container Registry 
- un stockage Azure Blob
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Azure Event Hub
- Azure Stream Analytics Pour plus d’informations ou pour afficher un exemple de facture, visitez la [page Tarification de Azure](https://azure.microsoft.com/pricing/details/machine-learning-services/). 
