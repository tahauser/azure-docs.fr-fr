---
title: Création de points de terminaison de service web dans Machine Learning | Microsoft Docs
description: Création de points de terminaison de service web dans Azure Machine Learning
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.openlocfilehash: fac284e9f0c852306d99733a879fc13c85f07768
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="creating-endpoints"></a>Création de points de terminaison
> [!NOTE]
>  Cette rubrique décrit les techniques applicables à un service web Machine Learning **classique**.
> 
> 

Lorsque vous créez des services web que vous vendez à vos clients, vous devez fournir à ceux-ci des modèles formés qui restent liés à l’expérience à partir de laquelle le service web a été créé. En outre, des mises à jour de l’expérience peuvent être appliquées de façon sélective à un point de terminaison, sans remplacer les personnalisations.

À cette fin, le Azure Machine Learning vous permet de créer plusieurs points de terminaison pour un service web déployé. Chaque point de terminaison du service web est adressé, limité et géré de façon indépendante. Chaque point de terminaison est une URL unique, et la clé d’autorisation que vous pouvez distribuer à vos clients.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>Ajout de points de terminaison à un service web
Il existe deux façons d’ajouter un point de terminaison à un service web.

* Par programmation
* Via le portail des services web Azure Machine Learning

Une fois le point de terminaison créé, vous pouvez l’exploiter via des API synchrones, des API de lot et des feuilles de calcul Microsoft Excel. Outre l'ajout de points de terminaison via cette interface utilisateur, vous pouvez également utiliser les API de gestion de point de terminaison pour ajouter de manière programmée des points de terminaison.

> [!NOTE]
> Si vous avez ajouté des points de terminaison au service web, vous ne pouvez pas supprimer le point de terminaison par défaut.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Ajout d’un point de terminaison par programme
Vous pouvez ajouter un point de terminaison à votre service web par programme en utilisant l’exemple de code [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Ajout d’un point de terminaison à l’aide du portail des services web Azure Machine Learning
1. Dans Machine Learning Studio, dans la colonne de navigation de gauche, cliquez sur Services web.
2. En bas du tableau de bord du service web, cliquez sur **Gérer les points de terminaison**. Le portail des services web Azure Machine Learning s’ouvre sur la page des points de terminaison pour le service web.
3. Cliquez sur **Nouveau**.
4. Tapez un nom et une description pour le point de terminaison. Les noms de point de terminaison doivent compter au maximum 24 caractères, et doivent être composés de lettres minuscules ou de chiffres. Sélectionnez le niveau de journalisation et activez les exemples de données si nécessaire. Pour plus d’informations sur la journalisation, voir [Activation de la journalisation pour les services web Machine Learning](web-services-logging.md).

## <a name="next-steps"></a>Étapes suivantes
[Utilisation d’un service web Azure Machine Learning déployé à partir d’une expérience Machine Learning](consume-web-services.md).

