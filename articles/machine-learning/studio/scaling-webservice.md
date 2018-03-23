---
title: Augmentation de l’accès concurrentiel d’un service web Azure Machine Learning | Microsoft Docs
description: Découvrez comment augmenter l’accès concurrentiel d’un service web Azure Machine Learning en ajoutant des points de terminaison.
services: machine-learning
documentationcenter: ''
author: aashishb
manager: srikants
editor: cgronlun
keywords: Azure Machine Learning, services web, opérationalisation, mise à l’échelle, point de terminaison, accès concurrentiel
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: aashishb
ms.openlocfilehash: bec69a273a9996782376196b193686ab8cfe1eaa
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Mise à l’échelle d’un service web Azure Machine Learning en ajoutant des points de terminaison
> [!NOTE]
> Cette rubrique décrit les techniques applicables à un service web Machine Learning **classique**. 
> 
> 

Par défaut, chaque service web publié est configuré pour prendre en charge 20 requêtes simultanées, avec un maximum de 200 requêtes. Azure Machine Learning optimise automatiquement ce paramètre pour améliorer les performances de votre service web et la valeur de portail est ignorée. 

Si vous envisagez d’appeler l’API avec une charge supérieure à ce que le nombre maximal d’appels simultanés (200) prend en charge, vous devez créer plusieurs points de terminaison sur le même service web. Vous pouvez ensuite répartir la charge entre tous de façon aléatoire.

La mise à l’échelle d’un service web est une tâche courante. Parmi les raisons d’effectuer une mise à l’échelle figurent la nécessité de prendre en charge plus de 200 demandes simultanées, d’augmenter la disponibilité via plusieurs points de terminaison, ou de fournir des points de terminaison distincts pour le service web. Vous pouvez augmenter l’échelle en ajoutant des points de terminaison supplémentaires pour le même service web via le portail du [service web Azure Machine Learning](https://services.azureml.net/).

Pour plus d’informations sur l’ajout de points de terminaison, voir [Création de points de terminaison](create-endpoint.md).

N’oubliez pas que l’utilisation d’un nombre élevé d’accès concurrentiels peut être préjudiciable si vous n’appelez pas l’API avec un taux tout aussi élevé. Vous pouvez constater des délais d’attente et/ou des pics de latence sporadiques si vous placez une charge relativement faible sur une API configurée pour une charge élevée.

Les API synchrones sont généralement utilisées dans les situations où vous souhaitez une latence faible. La latence implique ici le temps nécessaire à l’API pour compléter une demande et ne prend pas en compte les retards de réseau. Supposons que vous avez une API avec une latence de 50 millisecondes. Pour utiliser toute la capacité disponible avec le niveau de limitation élevé et le nombre maximal d’appels simultanés = 20, vous devez appeler cette API 20 * 1000 / 50 = 400 fois par seconde. De même, un nombre maximum d’appels simultanés de 200 vous permet d’appeler l’API 4000 fois par seconde, en supposant une latence de 50 millisecondes.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
