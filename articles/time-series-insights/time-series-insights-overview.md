---
title: "Qu’est-ce que Azure Time Series Insights ? | Microsoft Docs"
description: "Présentation d’Azure Time Series Insights, un nouveau service d’analyse de données de série chronologique et de solutions IoT."
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan, jasonh
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 01/26/2018
ms.openlocfilehash: e31cebfd027e93096e233f2963445e4fc50a7e9d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="what-is-azure-time-series-insights"></a>Qu’est-ce que Azure Time Series Insights ?

Time Series Insights est pensé pour le stockage, la visualisation et l’interrogation de grandes quantités de données de série chronologique, comme celles générées par les appareils IoT.  Si vous souhaitez stocker, gérer, interroger ou visualiser des données de série chronologique dans le cloud, Time Series Insights est probablement la solution que vous cherchez.  

![Organigramme Time Series Insights] (media/overview/time-series-insights-flowchart.png)

Time Series Insights a quatre tâches principales :

- Tout d’abord, il s’intègre entièrement à des passerelles cloud comme Azure IoT Hub et Azure Event Hubs. Il se connecte facilement à ces sources d’événements et analyse le JSON des messages et des structures qui comportent des données dans des colonnes et des lignes propres. Il joint des métadonnées aux données de télémétrie et indexe les données dans un magasin orienté colonnes.
- Ensuite, Time Series Insights gère le stockage des données. Pour que les données soient toujours faciles d’accès, il peut les stocker en mémoire et sur SSD jusqu’à 400 jours. Vous pouvez interroger de manière interactive des milliards d’événements en quelques secondes, à la demande.
- Troisièmement, Time Series Insights offre une visualisation prête à l’emploi grâce à l’explorateur TSI.  
- Quatrièmement, Time Series Insights propose un service de requête, à la fois dans l’explorateur TSI et par le biais d’API faciles à intégrer, pour intégrer les données de série chronologique dans des applications personnalisées.  

Si vous créez une application, en vue d’une utilisation en interne ou pour des clients externes, vous pouvez utiliser Time Series Insights comme serveur principal pour l’indexation, le stockage et l’agrégation des données de série chronologique. Vous pouvez créer une visualisation et une expérience utilisateur personnalisées en plus.  Time Series Insights expose des API Query pour permettre ce scénario.  

Si vous ne savez pas si vos données sont de série chronologique, voici ce que vous devez savoir.  Les données de série chronologique représentent la façon dont un élément multimédia ou un processus changent au fil du temps.  Elles sont uniques en cela qu’elles ont un horodatage et que l’heure est plus explicite sous forme d’axe.  En général, les données de série chronologique arrivent dans un ordre chronologique et sont considérées comme une instruction insert, plutôt qu’une mise à jour pour votre base de données.  Étant donné que Time Series Insights capture et stocke chaque nouvel événement sous la forme d’une ligne, le changement est mesuré dans le temps, ce qui vous permet de voir les valeurs passées et de prévoir les futurs changements.  Dans des volumes importants, le stockage, l’indexation, l’interrogation, l’analyse et la visualisation des données de série chronologique peuvent être difficiles.  

## <a name="primary-scenarios"></a>Principaux scénarios

- Stockage des données de série chronologique dans une méthode évolutive.  
  - À sa base, Time Series Insights a une base de données conçue avec les données de série chronologique à l’esprit.  Comme il est entièrement géré et évolutif, Time Series Insights gère les tâches de stockage et de gestion des événements.

- Exploration de données en quasi temps réel.  
  - Time Series Insights fournit un explorateur qui permet de visualiser toutes les données en streaming dans un environnement.  Peu après la connexion à une source d’événements, les données d’événement peuvent être affichées, explorées et interrogées dans Time Series Insights.  Les données sont utiles pour vérifier si un appareil émet des données comme prévu et pour surveiller une ressource IoT à des fins d’intégrité, de productivité et d’efficacité globale.  

- Analyse de la cause première et détection des anomalies.
  - Time Series Insights a des outils tels que les modèles et les vues de perspective pour effectuer et enregistrer des analyses des causes racine à plusieurs étapes.  En outre, Time Series Insights fonctionne conjointement avec des services de génération d’alertes tels qu’Azure Stream Analytics, ainsi les alertes et les anomalies détectées peuvent être affichées presque en temps réel dans l’Explorateur Time Series Insights.  

- Une vue globale du flux de données de série chronologique à partir d’emplacements différents pour la comparaison de plusieurs ressources/sites.
  - Vous pouvez vous connecter à plusieurs sources d’événements dans un environnement de Time Series Insights.  Cela signifie que les flux de données de plusieurs emplacements disparates peuvent être affichés ensemble quasiment en temps réel.  Les utilisateurs peuvent tirer parti de ce niveau de visibilité pour partager des données avec les responsables commerciaux et permettre une meilleure collaboration avec des experts du domaine qui peuvent appliquer leur expertise pour aider à résoudre les problèmes, appliquer les meilleures pratiques et partager des retours.

- Création d’une application client sur la base de Time Series Insights. 
  - Time Series Insights expose des API REST, ce qui vous permet de créer des applications qui utilisent des données de série chronologique.

## <a name="capabilities"></a>Fonctionnalités

- **Prise en main rapide** : Azure Time Series Insights ne requiert aucune préparation de données initiale. Se connecter à des millions d’événements dans votre IoT Hub Azure ou votre Event Hub en quelques minutes. Une fois connecté, visualisez et interagissez avec les données de capteurs pour valider rapidement vos solutions IoT. Vous pouvez interagir avec vos données sans écrire de code.
Vous n’avez pas besoin d’apprendre un nouveau langage. Time Series Insights propose aux utilisateurs expérimentés une zone de recherche de texte libre granulaire, ainsi qu’une expérience d’exploration « pointer et cliquer ».
- **Informations en temps presque réel** : Time Series Insights peut recevoir des millions d’événements de capteur par jour, avec une latence d’une minute. Time Series Insights vous aide à obtenir des informations sur vos données de capteur en vous aidant à identifier rapidement les tendances et anomalies, à effectuer des analyses de la cause première et à éviter des interruptions de service coûteuses. En proposant une corrélation croisée entre données en temps réel et données historiques, Time Series Insights vous aide à détecter les tendances cachées dans leurs données.
- **Créer des solutions personnalisées** : intégrez les données Azure Time Series Insights à vos applications existantes ou créez de nouvelles solutions personnalisées avec les API REST de Time Series Insights. Créez des affichages personnalisés que vous pouvez partager afin que d’autres personnes puissent explorer vos analyses.
- **Extensibilité** : Time Series Insights est conçu pour prendre en charge IoT une fois mis à l’échelle. De 1 à 100 millions d’événements peuvent être entrés par jour, avec une durée de conservation par défaut de 31 jours. Vous pouvez visualiser et analyser les flux de données en direct en temps presque réel, en plus des données historiques. À l’avenir, les taux d’entrée et de conservation seront augmentés pour s’adapter à l’évolution de l’entreprise.

## <a name="getting-started"></a>Prise en main
La prise en main prend moins de 5 minutes. 

1.  Pour commencer, approvisionnez un environnement Time Series Insights dans le portail Azure. 
2.  Connectez-vous à une source d’événements comme une instance Azure IoT Hub ou un concentrateur d’événements.  
3.  Téléchargez les données de référence (cela n’est pas un service supplémentaire).
4.  Consultez vos données en quelques minutes avec l’Explorateur Time Series Insights.

## <a name="time-series-insights-explorer"></a>Explorateur Time Series Insights
Ce diagramme illustre un exemple de données de série chronologique Time Series Insights affichées au moyen de l’Explorateur : ![Time Series Insights explorer] (media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Étapes suivantes
 - [Découvrir l’Explorateur Time Series Insights dans un environnement de démonstration](./time-series-quickstart.md)
 - [Sélectionner votre environnement Time Series Insights](time-series-insights-environment-planning.md)

