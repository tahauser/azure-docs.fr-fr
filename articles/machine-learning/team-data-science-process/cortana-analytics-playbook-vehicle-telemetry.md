---
title: Prédire l’état des véhicules et les habitudes de conduite | Microsoft Docs
description: Utilisez les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: bradsev
ms.openlocfilehash: 02b3e0e0808cb9a1a8a2186b1abe6da7dd13e56e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Manuel de la solution Vehicle Telemetry Analytics
Ce menu contient des liens vers les chapitres de ce manuel : 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Vue d'ensemble
Aujourd’hui, les superordinateurs ne se trouvent plus dans des laboratoires mais dans des garages. Ils sont désormais placés dans des automobiles de pointe qui contiennent une multitude de capteurs. Ce capteurs leur offrent la possibilité de suivre et d’analyser des millions d’événements par seconde. D’ici 2020, la plupart de ces véhicules seront connectés à Internet. Le potentiel qu’offrent toutes ces données permet d’améliorer la sécurité, la fiabilité et le plaisir de la conduite. Microsoft fait de ce rêve une réalité en développant Cortana Intelligence.

Cortana Intelligence est une suite de traitement du Big Data et d’analyse avancée entièrement gérée, qui vous permet de convertir vos données en action intelligente. Le modèle de la solution Cortana Intelligence Vehicle Telemetry Analytics montre comment les concessionnaires, les constructeurs automobiles et les compagnies d’assurance peuvent obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite.

La solution est implémentée comme un [modèle d’architecture lambda](https://en.wikipedia.org/wiki/Lambda_architecture) montrant tout le potentiel de la plateforme Cortana Intelligence pour le traitement en temps réel et par lots.

## <a name="architecture"></a>Architecture
L’architecture de la solution Vehicle Telemetry Analytics est présentée dans ce diagramme :

![Diagramme de l’architecture de la solution](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Cette solution inclut les composants Cortana Intelligence suivants et présente leur intégration :

* **Azure Event Hubs** ingère dans Azure des millions d’événements de télémétrie associés aux véhicules.
* **Azure Stream Analytics** fournit une visibilité en temps réel sur l’état des véhicules et conserve ces données dans un stockage à long terme afin d’enrichir les analyses par lots.
* **Azure Machine Learning** détecte les anomalies en temps réel et utilise le traitement par lots pour fournir des informations prédictives.
* **Azure HDInsight** transforme les données à grande échelle.
* **Azure Data Factory** gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots.
* **POWER BI** offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

Cette solution utilise deux sources de données différentes : 

* **SIMULATION DES SIGNAUX ET DIAGNOSTICS D’UN VÉHICULE**: un simulateur télématique de véhicule émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné dans le temps. 
* **Catalogue de véhicules** : ce jeu de données de référence associe les numéros d’identification du véhicule aux modèles.

