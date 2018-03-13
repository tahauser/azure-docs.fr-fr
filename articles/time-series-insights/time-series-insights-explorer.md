---
title: "Explorer des données avec l’explorateur Azure Time Series Insights | Microsoft Docs"
description: "Cet article décrit comment utiliser l’Explorateur Azure Time Series Insights dans votre navigateur web pour afficher rapidement une vue globale de vos données et valider votre environnement IoT."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: kfile
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/30/2017
ms.openlocfilehash: d09292cce1414a1b89e4b75df27d0a689738b4d6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-time-series-insights-explorer"></a>Explorateur Azure Time Series Insights
Cet article explore les différentes fonctionnalités et options disponibles dans l’application web de l’Explorateur Time Series Insights. Vous pouvez utiliser l’explorateur Time Series Insights dans votre navigateur web pour créer des visualisations de vos données.
 
Azure Time Series Insights est un service entièrement managé d’analyse, de stockage et de visualisation qui simplifie la découverte et l’analyse simultanées de milliards d’événements IoT. Cette solution vous donne une vue globale de vos données, ce qui vous permet de valider rapidement votre solution IoT et d’éviter des temps morts coûteux d’appareils stratégiques. Vous pouvez découvrir des tendances masquées, détecter les anomalies et effectuer des analyses de cause première quasiment en temps réel. L’Explorateur Time Series Insights est actuellement en version préliminaire publique.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir utiliser l’Explorateur Time Series Insights, vous devez :
- Créer un environnement Time Series Insights
- Fournir l’accès à votre compte dans l’environnement
- Ajouter une source d’événement pour la réception des données et la stocker

## <a name="explore-and-query-data"></a>Explorer et interroger les données
Après quelques minutes suite à la connexion de votre source d’événements à votre environnement Time Series Insights, vous pouvez explorer et interroger vos données de série chronologique.

1. Pour commencer, ouvrez [l’explorateur Time Series Insights](https://insights.timeseries.azure.com/) dans votre navigateur web, puis sélectionnez un environnement sur le côté gauche de la fenêtre. Tous les environnements auxquels vous avez accès sont répertoriés par ordre alphabétique.

2. Une fois que vous sélectionnez un environnement, utilisez les configurations **FROM** et **TO** en haut, ou cliquez et faites glisser votre intervalle de temps souhaité.  Cliquez sur la loupe en haut à droite, ou avec le bouton droit sur l’intervalle de temps sélectionné et sélectionnez **Rechercher**.  

3. Vous pouvez également actualiser la disponibilité automatiquement toutes les minutes, en sélectionnant le **Activer automatiquement**.  Notez que le bouton « Activer automatiquement » s’applique uniquement au graphique de disponibilité, pas au contenu de la visualisation principale.

4. Notez que l’icône Azure Cloud vous permet d’accéder à votre environnement dans le portail Azure.

   ![Environnement Time Series Insights](media/time-series-insights-explorer/explorer1.png)

5. Ensuite, vous voyez un graphique qui affiche le nombre total d’événements pendant la période sélectionnée.  Vous avez plusieurs commandes disponibles :

    **Panneau d’éditeur de conditions** : L’espace de terme est là où vous interrogez votre environnement.  Vous trouverez cela sur le côté gauche de l’écran, active 
      - **Mesure** : Cette liste déroulante affiche toutes les colonnes numériques (doubles)
      - **Fractionner par** : Cette liste déroulante affiche les colonnes catégorielles (chaînes)
      - Vous pouvez activer une interpolation par étape, afficher les valeurs minimale et maximale et ajuster l’axe des ordonnées à partir du panneau suivant pour mesurer.  En outre, vous pouvez choisir si les données indiquées sont un nombre, une moyenne ou une somme des données.
      - Vous pouvez ajouter jusqu'à cinq conditions à afficher sur l’axe des abscisses.  Utilisez le bouton **Copier** bouton pour ajouter un terme supplémentaire, ou cliquez sur le bouton **Ajouter** pour ajouter un nouveau terme.
     
        ![Panneau de l’éditeur de conditions](media/time-series-insights-explorer/explorer2.png)

      - **Prédicat** : Le prédicat vous permet de filtrer rapidement les événements à l’aide de l’ensemble d’opérandes ci-dessous. Si vous effectuez une recherche en la sélectionnant ou en cliquant dessus, le prédicat est automatiquement mis à jour selon cette recherche.      Les types d’opérandes pris en charge comprennent les suivants :

         |Opération  |Types pris en charge  |Notes  |
         |---------|---------|---------|
         |<, >, <=, >=     |  Double, DateTime, TimeSpan       |         |
         |=, !=, <>     | Chaîne, Bool, Double, DateTime, TimeSpan, NULL        |         |
         |IN     | Chaîne, Bool, Double, DateTime, TimeSpan, NULL        |  Tous les opérandes doivent être du même type ou être la constante NULL.        |
         |HAS     | Chaîne        |  Seuls les littéraux de chaîne constante sont autorisés à droite. Les chaînes vides et NULL ne sont pas autorisés.       |

      - **Exemples de requêtes**
      
         ![Exemples de requêtes](media/time-series-insights-explorer/explorer9.png)

6. L’outil curseur **Taille de l’intervalle** vous permet d’effectuer un zoom/zoom arrière sur les intervalles pour la même plage de dates.  Cela fournit un contrôle plus précis du déplacement entre les tranches de temps volumineuses qui montrent les tendances lissées, jusqu'à tranches aussi petites que la milliseconde, ce qui vous permet de voir des morceaux granulaires, haute résolution de vos données. Le point de départ par défaut du curseur est défini comme la vue optimale des données à partir de votre sélection ; l’équilibrage de la résolution, la vitesse de la requête et la granularité.

7. L’outil **Pinceau horaire** vous permet de naviguer d’un intervalle de temps à un autre, pour une expérience intuitive et transparente lorsque vous vous déplacez entre les intervalles de temps.

8. La commande **Enregistrer** vous permet d’enregistrer votre requête actuelle et d’activer le partage avec d’autres utilisateurs de l’environnement. À l’aide du bouton **Ouvrir**, vous pouvez voir toutes vos requêtes enregistrées et toutes les requêtes partagées des autres utilisateurs dans les environnements auxquels vous avez accès. 

   ![Requêtes](media/time-series-insights-explorer/explorer3.png)

9. L’outil **Vue en perspective** fournit une vue simultanée de jusqu'à quatre requêtes uniques. Le bouton de la vue en perspective se situe dans le coin supérieur droit du graphique.  

   ![Vue en perspective](media/time-series-insights-explorer/explorer4.png)

10. Le **Graphique** vous permet d’explorer visuellement vos données. Les outils de graphique comprennent :

   - Sélectionnez/Cliquez, ce qui permet une sélection d’un intervalle de temps spécifique ou d’une seule série de données.  
   - Dans une sélection d’intervalle, vous pouvez effectuer un zoom ou explorer les événements.  
   - Au sein d’une série de données, vous pouvez fractionner la série par une autre colonne, ajouter la série en tant que nouveau terme, afficher uniquement la série sélectionnée, exclure les séries sélectionnées, effectuer un test ping sur cette série ou explorer les événements de la série sélectionnée.
   - Dans la zone de filtre à gauche du graphique, vous pouvez voir toutes les séries de données affichées et les réorganiser par valeur ou par nom, afficher toutes les séries de données ou des séries épinglées ou non épinglées spécifiquement.  Vous pouvez également sélectionner une seule série de données et fractionner la série par une autre colonne, ajouter la série en tant que nouveau terme, afficher uniquement la série sélectionnée, exclure les séries sélectionnées, effectuer un test ping sur cette série ou explorer les événements de la série sélectionnée.
   - Lorsque vous affichez plusieurs termes simultanément, vous pouvez empiler, désempiler et voir des données supplémentaires sur une série de données et utiliser le même axe des ordonnées sur tous les termes du contrat avec les boutons dans le coin supérieur droit du graphique.
 
   ![Outil de graphique](media/time-series-insights-explorer/explorer5.png) 

11. La **carte thermique** peut être utilisée pour identifier rapidement les séries de données uniques ou anormales dans une requête donnée. Un seul terme de recherche peut être visualisé comme une carte thermique.    

   ![Carte thermique](media/time-series-insights-explorer/explorer6.png)

12. **Événements** : Lorsque vous choisissez d’explorer les événements lors de la sélection ou cliquant dessus, le panneau d’événements est rendu disponible.  Ici, vous pouvez voir tous les événements bruts et exporter vos événements sous forme de fichiers JSON ou CSV. Notez que Time Series Insights stocke toutes les données brutes.

   ![Événements](media/time-series-insights-explorer/explorer7.png)

13. Cliquez sur l’onglet **Statistiques** après avoir exploré les événements pour exposer des modèles et les statistiques de colonne.  

   - **Modèles** : Cette fonctionnalité fait ressortir de façon proactive les modèles statistiquement les plus significatifs dans une région de données sélectionnée. Cela vous évite de devoir examiner plusieurs milliers d’événements pour comprendre les modèles qui justifient le plus de temps et d’énergie. En outre, Time Series Insights vous permet d’accéder directement à ces modèles statistiquement significatifs pour continuer la réalisation d’une analyse. Cette fonctionnalité est également utile pour les enquêtes post mortem des données historiques. 

   - **Statistiques de colonne** : Les statistiques de colonne fournissent des graphiques et des tables qui décomposent les données de chaque colonne de la série de données sélectionnée sur l’intervalle de temps sélectionné.  
 
      ![STATS](media/time-series-insights-explorer/explorer8.png) 

Vous avez maintenant vu les différentes fonctionnalités et options disponibles dans l’application web de l’Explorateur Time Series Insights. 

## <a name="next-steps"></a>étapes suivantes
> [!div class="nextstepaction"]
>[Diagnostiquer et résoudre les problèmes dans votre environnement Time Series Insights](time-series-insights-diagnose-and-solve-problems.md)
