---
title: "Analyse d’événements Azure Service Fabric avec OMS | Microsoft Docs"
description: "Découvrez l’analyse et la visualisation d’événements à l’aide d’OMS pour la surveillance et le diagnostic de clusters Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 977c5d64a32157b39aa6b618196dde20c4c3cc8e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Analyse et visualisation d’événements avec OMS

OMS (Operations Management Suite) est une collection de services de gestion liés à la surveillance et au diagnostic d’applications et de services hébergés dans le cloud. Pour obtenir une présentation plus détaillée d’OMS et de ce qu’il propose, lisez [Qu’est-ce qu’OMS ?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics et l’espace de travail OMS

Log Analytics collecte des données à partir de ressources gérées, notamment un agent ou une table de stockage Azure, et les conserve dans un référentiel central. Les données peuvent ensuite être utilisées pour l’analyse, l’alerte et la visualisation, ou une nouvelle exportation. Log Analytics prend en charge les événements, les données de performances ou toute autre donnée personnalisée.

Quand OMS est configuré, vous avez accès à un *espace de travail OMS* spécifique, à partir duquel les données peuvent être interrogées ou visualisées dans des tableaux de bord.

Après réception des données par Log Analytics, OMS dispose de plusieurs *solutions de gestion* qui sont des solutions prépackagées permettant de surveiller les données entrantes, personnalisées pour plusieurs scénarios. Ces solutions incluent une solution *Service Fabric Analytics* et une solution *Conteneurs*, qui sont les deux solutions les plus appropriées pour les diagnostics et le suivi lors de l’utilisation des clusters Service Fabric. Il en existe aussi plusieurs autres qu’il est conseillé d’explorer. OMS permet également de créer des solutions personnalisées. Vous trouverez plus d’informations à ce sujet [ici](../operations-management-suite/operations-management-suite-solutions.md). Chaque solution que vous choisissez d’utiliser pour un cluster peut être configurée dans le même espace de travail OMS, avec Log Analytics. Les espaces de travail autorisent les tableaux de bord personnalisés et la visualisation de données, ainsi que les modifications des données que vous voulez collecter, traiter et analyser.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Configuration d’un espace de travail OMS avec la solution Service Fabric Analytics
Il est recommandé d’inclure la solution Service Fabric dans votre espace de travail OMS, car elle fournit un tableau de bord qui montre les différents canaux de journaux entrants du niveau plateforme et application, ainsi que la possibilité d’interroger des journaux Service Fabric spécifiques. Voici à quoi ressemble une solution Service Fabric relativement simple, avec une seule application déployée sur le cluster :

![Solution SF OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Pour utiliser cette solution avec votre cluster, consultez [Configurer OMS Log Analytics](service-fabric-diagnostics-oms-setup.md).

## <a name="using-the-oms-agent"></a>Utilisation de l’agent OMS

Il est recommandé d’utiliser EventFlow et WAD comme solutions d’agrégation, car ils permettent d’adopter une approche plus modulable des diagnostics et du suivi. Par exemple si vous voulez changer vos sorties à partir d’EventFlow, aucune modification de votre instrumentation réelle n’est nécessaire, mais juste une simple modification de votre fichier de configuration. Toutefois, si vous décidez d’utiliser OMS Log Analytics, vous devez configurer [l’agent OMS](../log-analytics/log-analytics-windows-agent.md). Vous devez également utiliser l’agent OMS lors du déploiement de conteneurs sur votre cluster, comme indiqué ci-dessous. 

Pour obtenir des instructions, consultez [Ajouter l’agent OMS à un cluster](service-fabric-diagnostics-oms-agent.md).

Les avantages de cette approche sont les suivants :

* Données enrichies du côté des compteurs de performances et des indicateurs de performance.
* Métriques faciles à configurer collectées à partir du cluster et sans avoir à mettre à jour la configuration de votre cluster. Les modifications apportées aux paramètres de l’agent peuvent être effectuées à partir du portail OMS et l’agent redémarre automatiquement pour correspondre à la configuration requise. Pour configurer l’agent OMS afin de récupérer des compteurs de performance spécifiques, accédez à la **Page d’accueil de l’espace de travail > Paramètres > Données > Compteurs de performances Windows**, puis sélectionnez les données que vous voulez collecter.
* Les données s’affichent plus rapidement que si elles devaient être stockées avant d’être récupérées par OMS/Log Analytics.
* La surveillance des conteneurs est beaucoup plus facile, car elle peut sélectionner les journaux Docker (stdout, stderr) et les statistiques (métriques de performance au niveau des conteneurs et des nœuds)

La principale considération ici est que, comme l’agent sera déployé sur votre cluster avec toutes vos applications, les performances de vos applications sur le cluster peuvent être affectées.

## <a name="monitoring-containers"></a>Surveillance des conteneurs

Quand vous déployez des conteneurs dans un cluster Service Fabric, il est recommandé de configurer le cluster avec l’agent OMS et d’ajouter la solution Conteneurs à votre espace de travail OMS pour permettre le suivi et les diagnostics. Voici à quoi ressemble la solution Conteneurs dans un espace de travail :

![Tableau de bord OMS de base](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L’agent permet de collecter plusieurs journaux spécifiques aux conteneurs qui peuvent être interrogés dans OMS ou utilisés pour visualiser les indicateurs de performance. Les types de journaux collectés sont :

* ContainerInventory : affiche des informations sur les images, le nom et l’emplacement du conteneur
* ContainerImageInventory : informations sur les images déployées, ID ou tailles compris
* ContainerLog : journaux d’erreurs spécifiques, journaux de docker (stdout, etc.) et autres entrées
* ContainerServiceLog : les commandes de démon docker qui ont été exécutées
* Performances : les compteurs de performances, dont l’utilisation par le conteneur du processeur, de la mémoire, du E/S de disque, du trafic réseau et de mesures personnalisées à partir des machines hôtes

L’article [Surveiller les conteneurs avec OMS Log Analytics](service-fabric-diagnostics-oms-containers.md) décrit les étapes nécessaires à la configuration de la surveillance de conteneur pour votre cluster. Pour en savoir plus sur la solution de conteneurs d’OMS, consultez leur [documentation](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Étapes suivantes

Explorez les outils et options OMS suivants pour personnaliser un espace de travail en fonction de vos besoins :

* Pour les clusters locaux, OMS propose une passerelle (proxy de transfert HTTP) qui peut être utilisée pour envoyer des données à OMS. Pour plus d’informations à ce sujet, consultez [Connexion d’ordinateurs à OMS sans accès Internet à l’aide de la passerelle OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configurez OMS pour configurer l’[alerte automatisée](../log-analytics/log-analytics-alerts.md) afin de faciliter la détection et les diagnostics
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics