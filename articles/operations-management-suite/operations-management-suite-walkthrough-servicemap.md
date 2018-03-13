---
title: "Démonstration de la solution Service Map à votre rythme | Microsoft Docs"
description: "Service Map est une solution comprise dans Azure qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services.  Il s’agit d’une démonstration qui vous permet de découvrir à votre rythme l’utilisation de Service Map pour identifier et diagnostiquer un problème simulé dans une application web."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: 35fe4e95eae8b63425abc8ed2970c0ad51073883
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="self-paced-demo---service-map"></a>Démonstration de la solution Service Map à votre rythme
Il s’agit d’une démonstration qui vous permet de découvrir à votre rythme l’utilisation de [Service Map](operations-management-suite-service-map.md) pour identifier et diagnostiquer un problème simulé dans une application web.  La solution Service Map détecte automatiquement les composants d’application sur les systèmes Windows et Linux, et mappe la communication entre les services.  Il regroupe également les données collectées par les autres services et solutions pour vous aider à analyser les performances et identifier les problèmes.  Vous utiliserez également [Recherches de journal dans Log Analytics](../log-analytics/log-analytics-log-searches.md) pour détailler les données collectées afin d’identifier l’origine du problème.


## <a name="scenario-description"></a>Description du scénario
Vous venez de recevoir une notification vous indiquant que l’application ACME Customer Portal rencontre des problèmes de performances.  La seule information dont vous disposez est que ces problèmes ont démarré vers 4 h 00 PST aujourd’hui.  Vous n’êtes pas tout à fait certain de tous les composants dont dépend le portail à part un ensemble de serveurs web.  

## <a name="components-and-features-used"></a>Composants et fonctionnalités utilisés
- [Solution Carte de service](operations-management-suite-service-map.md)
- [Recherches de journal Log Analytics](../log-analytics/log-analytics-log-searches.md)


## <a name="walk-through"></a>Procédure

### <a name="1-connect-to-the-oms-experience-center"></a>1. Connexion à l’OMS Experience Center (centre d’expérience OMS)
Ce guide utilise [l’Operations Management Suite Experience Center (centre d’expérience Operations Management Suite)](https://experience.mms.microsoft.com/) qui fournit un environnement Log Analytics complet avec des exemples de données. Commencez par cliquer sur ce lien, fournissez vos informations, puis sélectionnez le scénario **Insight and Analytics**.


### <a name="2-start-service-map"></a>2. Lancement de Carte de service
Démarrez la solution Carte de Service en cliquant sur la vignette **Carte de Service**.

![Vignette Carte de service](media/operations-management-suite-walkthrough-servicemap/tile.png)

La console Carte de service s’affiche.  Le volet gauche présente la liste des ordinateurs de votre environnement ayant l’agent Carte de service installé.  Sélectionnez l’ordinateur que vous souhaitez afficher depuis cette liste.

![Liste des ordinateurs](media/operations-management-suite-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Affichage de l’ordinateur
Nous savons que les serveurs web sont appelés AcmeWFE001 et AcmeWFE002, ce qui représente un bon point de départ.  Cliquez sur **AcmeWFE001**.  La carte de AcmeWFE001 et de toutes ses dépendances s’affiche.  Vous pouvez voir quels processus sont en cours d’exécution sur l’ordinateur sélectionné et avec quels services externes ils communiquent.

![Serveur web](media/operations-management-suite-walkthrough-servicemap/web-server.png)

Nous nous attachons aux performances de notre application web, donc cliquez sur le processus **AcmeAppPool (pool d’applications IIS)**.  Les détails de ce processus s’affichent et mettent en évidence ses dépendances.  

![Pool d’applications](media/operations-management-suite-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Modification de la fenêtre de temps

Nous savons que le problème a démarré à 4 h 00, par conséquent, nous allons donc chercher ce qui était en cours d’exécution à ce moment-là. Cliquez sur **Période** et modifiez l’heure à 4 h 00 PST (conservez la date actuelle et ajustez-la à votre fuseau horaire local) avec une durée de 20 minutes.

![Sélecteur d’heure](./media/operations-management-suite-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Affichage de l’alerte

Nous voyons à présent que la dépendance **acmetomcat** a une alerte affichée ; il s’agit de notre problème potentiel.  Cliquez sur l’icône d’alerte dans **acmetomcat** pour afficher les détails de l’alerte.  Nous pouvons constater une utilisation critique du processeur et nous pouvons développer pour obtenir plus de détails.  C’est probablement la cause de notre ralentissement des performances. 

![Alerte](./media/operations-management-suite-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Affichage des performances

Regardons **acmetomcat** de plus près.  Cliquez en haut à droite de **acmetomcat** et sélectionnez **Charger la carte du serveur** pour afficher les détails et les dépendances de cet ordinateur. Nous pouvons ensuite examiner davantage les compteurs de performances pour vérifier notre suspicion.  Sélectionnez l’onglet **Performances** pour afficher les [compteurs de performances collectés par Log Analytics](../log-analytics/log-analytics-data-sources-performance-counters.md) dans la plage de temps.  Nous constatons des pics périodiques au niveau du processeur et de la mémoire.

![Performances](./media/operations-management-suite-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Affichage du suivi des modifications
Voyons si nous pouvons trouver les causes possibles de cette utilisation intensive.  Cliquez sur l’onglet **Résumé**.  Cela fournit des informations que Log Analytics a collectées à partir de l’ordinateur, telles que les échecs de connexion, les alertes critiques et les modifications logicielles.  Les sections contenant des informations récentes intéressantes devraient déjà être développées, et vous pouvez développer les sections suivantes pour examiner les informations qu’elles contiennent.


Si **Suivi des modifications** n’est pas déjà ouvert, développez-le.  Cela permet d’afficher les informations collectées par la [solution Suivi des modifications](../log-analytics/log-analytics-change-tracking.md).  Il semblerait qu’une modification logicielle ait été effectuée dans cette fenêtre de temps.  Cliquez sur **Logiciel** pour obtenir des détails.  Un processus de sauvegarde a été ajouté à l’ordinateur juste après 4 h 00 du matin, ce qui semblerait être la cause de l’utilisation des ressources excessives.

![Suivi des modifications](./media/operations-management-suite-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Affichage des détails dans Recherche dans les journaux
Nous pouvons poursuivre la vérification en examinant les informations détaillées sur les performances collectées dans l’espace de travail Log Analytics.  Cliquez à nouveau sur l’onglet **Alertes**, puis sur l’une des alertes **High CPU** (utilisation élevée du processeur).  Cliquez sur **Show in Log Search (Afficher dans Recherche dans les journaux)**.  Cela ouvre la fenêtre Recherche dans les journaux dans laquelle vous pouvez effectuer des [recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) sur toutes les données stockées dans l’espace de travail.  Service Map a déjà rempli une requête pour nous permettre de récupérer l’alerte qui nous intéresse.  

![Recherche dans les journaux](./media/operations-management-suite-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Ouverture d’une recherche enregistrée
Voyons si nous pouvons obtenir d’autres détails sur la collecte des performances qui a généré cette alerte et vérifier notre suspicion selon laquelle les problèmes ont été provoqués par ce processus de sauvegarde.  Modifiez la période à **6 heures**.  Cliquez ensuite sur **Favoris** et faites défiler jusqu’aux recherches enregistrées pour **Carte de service**.  Nous avons créé ces requêtes spécifiquement pour cette analyse.  Cliquez sur **Top 5 Processes by CPU for acmetomcat (5 premiers processus par processeur pour acmetomcat)**.

![Recherche enregistrée](./media/operations-management-suite-walkthrough-servicemap/saved-search.png)


Cette requête retourne la liste des 5 premiers processus utilisant le processeur sur **acmetomcat**.  Vous pouvez inspecter la requête pour obtenir une présentation du langage de requête utilisé pour les recherches dans les journaux.  Si vous êtes intéressé par les processus sur d’autres ordinateurs, vous pouvez modifier la requête pour récupérer ces informations.

Dans ce cas, nous pouvons voir que le processus de sauvegarde consomme de façon cohérente environ 60 % de l’UC du serveur de l’application.  Il apparaît clairement que ce nouveau processus est responsable de notre problème de performances.  Notre solution serait évidemment de supprimer ce nouveau logiciel de sauvegarde du serveur d’applications.  Nous pouvons en fait utiliser la configuration d’état souhaité (DSC) gérée par Azure Automation pour définir des stratégies qui garantissent que ce processus ne s’exécute jamais sur ces systèmes critiques.


## <a name="summary-points"></a>Résumé
- [Carte de service](operations-management-suite-service-map.md) vous fournit une vue de l’intégralité de votre application même si vous ne connaissez pas tous ses serveurs et dépendances.
- Service Map expose les données collectées par les autres solutions de gestion pour vous aider à identifier les problèmes avec votre application et son infrastructure sous-jacente.
- [Recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) vous permet de consulter des données spécifiques recueillies dans l’espace de travail Log Analytics.    

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Carte de service](operations-management-suite-service-map.md).
- [Déployer et configurer](operations-management-suite-service-map-configure.md) Carte de service.
- En savoir plus sur [Log Analytics](../log-analytics/log-analytics-overview.md) qui est requis pour Service Map et conserve les données opérationnelles stockées par les agents.