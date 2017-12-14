---
title: "Mise à niveau d’application Service Fabric | Microsoft Docs"
description: "Cet article fournit une introduction à la mise à niveau d'une application Service Fabric, y compris le choix des modes de mise à niveau et les vérifications d'intégrité exécutées."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 5fed3b5b127a2b398b99ab2b46c762920e9dc249
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="service-fabric-application-upgrade"></a>Mise à niveau des applications Service Fabric
Une application Azure Service Fabric est une collection de services. Pendant une mise à niveau, Service Fabric compare le nouveau [manifeste d'application](service-fabric-application-and-service-manifests.md) à la version précédente et détermine les services qui, dans l'application, nécessitent des mises à jour. Service Fabric compare les numéros des versions dans les manifestes de service avec les numéros des versions dans la version précédente. Si un service n'a pas changé, ce service n'est pas mis à niveau.

## <a name="rolling-upgrades-overview"></a>Vue d'ensemble des mises à niveau propagées
Une mise à niveau d'application propagée s'effectue par étapes. À chaque étape, la mise à niveau est appliquée à un sous-ensemble de nœuds du cluster, appelé domaine de mise à jour. Ainsi, l'application demeure disponible tout au long de la mise à niveau. Au cours de la mise à niveau, le cluster peut contenir une combinaison des versions ancienne et nouvelle.

De ce fait, les deux versions doivent être mutuellement compatibles. Si ce n'est pas le cas, l'administrateur d'application doit effectuer une mise à niveau échelonnée pour maintenir la disponibilité. Dans une mise à niveau à plusieurs phases, la première étape consiste à effectuer une mise à niveau vers une version intermédiaire de l’application qui est compatible avec la version précédente. La deuxième étape consiste à mettre à niveau la version finale qui interrompt la compatibilité avec la version antérieure à la mise à jour, mais qui est compatible avec la version intermédiaire.

Les domaines de mise à jour sont spécifiés dans le manifeste de cluster quand vous configurez le cluster. Les domaines de mise à jour ne reçoivent pas les mises à jour dans un ordre particulier. Un domaine de mise à jour est une unité logique de déploiement pour une application. Grâce aux domaines de mise à jour, les services demeurent hautement disponibles pendant une mise à niveau.

Une mise à niveau non propagée est possible si elle est appliquée à tous les nœuds du cluster, ce qui est le cas quand l'application n'a qu'un seul domaine de mise à jour. Cette approche n’est pas recommandée, car le service est arrêté et indisponible au moment de la mise à niveau. En outre, Azure ne fournit aucune garantie quand un cluster est configuré avec un seul domaine de mise à jour.

Une fois la mise à niveau terminée, tous les services et les réplicas (instances) restent dans la même version ; en d’autres termes, si la mise à niveau réussit, ils sont mis à jour vers la nouvelle version, alors que si la mise à niveau échoue et est restaurée, ils sont restaurés vers l’ancienne version.

## <a name="health-checks-during-upgrades"></a>Vérifications d'intégrité pendant les mises à niveau
Pour une mise à niveau, vous devez définir des stratégies d'intégrité (ou utiliser éventuellement des valeurs par défaut). Une mise à niveau est considérée comme ayant réussi quand tous les domaines de mise à jour sont mis à niveau dans les délais spécifiés et que tous les domaines de mise à jour sont considérés comme intègres.  Un domaine de mise à jour intègre signifie que le domaine de mise à jour a réussi toutes les vérifications d'intégrité spécifiées dans la stratégie de contrôle d'intégrité. Par exemple, une stratégie d'intégrité peut imposer que tous les services dans une instance d'application doivent être *intègres*, car l'intégrité est définie par Service Fabric.

Pendant une mise à jour effectuée par Service Fabric, les stratégies et vérifications d'intégrité sont indépendantes du service et de l'application. Autrement dit, aucun test spécifique au service n'est exécuté.  Par exemple, votre service peut avoir une exigence de débit, mais Service Fabric ne dispose pas des informations pour vérifier le débit. Consultez les [articles sur l’intégrité](service-fabric-health-introduction.md) pour connaître les vérifications qui sont effectuées. Les contrôles qui se produisent pendant une mise à niveau incluent des tests pour savoir si le package d'application a été copié correctement, si l'instance a été démarrée, etc.

L'intégrité de l'application est un regroupement des entités enfants de l'application. En bref, Service Fabric évalue l'intégrité de l'application au travers de l'intégrité établie au sujet de celle-ci. Il évalue également l'intégrité de la totalité des services de l'application de cette façon. De plus, Service Fabric évalue l'intégrité des services d'application par l'agrégation de l'intégrité de leurs enfants, tels que le réplica de service. Une fois que la stratégie d'intégrité d'application est satisfaite, la mise à niveau peut continuer. Si la stratégie d'intégrité n'est pas respectée, la mise à niveau de l'application échoue.

## <a name="upgrade-modes"></a>Modes de mise à niveau
Le mode que nous recommandons pour la mise à niveau d’application est le mode surveillé, qui est couramment utilisé. Le mode surveillé effectue la mise à niveau sur un domaine de mise à jour spécifique, puis, si toutes les vérifications d'intégrité sont satisfaites (suivant la stratégie spécifiée), il passe automatiquement au domaine de mise à jour suivant.  Si les vérifications d’intégrité échouent et/ou que les délais d’expiration sont atteints, la mise à niveau est restaurée pour le domaine de mise à jour ou le système bascule sur le mode manuel non surveillé. Vous pouvez configurer la mise à niveau pour choisir l’un de ces deux modes pour les mises à niveau ayant échoué. 

Le mode manuel non surveillé nécessite une intervention manuelle après chaque mise à niveau sur un domaine de mise à jour pour lancer la mise à niveau sur le domaine de mise à jour suivant. Aucune vérification de l'intégrité de Service Fabric n'est effectuée. L'administrateur effectue les vérifications d'intégrité ou d'état avant de commencer la mise à niveau dans le domaine de mise à jour suivant.

## <a name="upgrade-default-services"></a>Mettre à niveau les services par défaut
Il est possible de mettre à niveau les services par défaut dans l’application Service Fabric pendant le processus de mise à niveau d’une application. Les services par défaut sont définis dans le [manifeste de l’application](service-fabric-application-and-service-manifests.md). Les règles standard de mise à niveau des services par défaut sont les suivantes :

1. Les services par défaut du nouveau [manifeste de l’application](service-fabric-application-and-service-manifests.md) qui n’existent pas dans le cluster sont créés.
> [!TIP]
> [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) doit avoir la valeur true pour activer les règles suivantes. Cette fonctionnalité est prise en charge à partir de la version 5.5.

2. Les services par défaut présents à la fois dans le [manifeste de l’application](service-fabric-application-and-service-manifests.md) précédent et dans la nouvelle version sont mis à jour. Les descriptions de service dans la nouvelle version remplaceraient celles qui se trouvent déjà dans le cluster. La mise à niveau de l’application serait restaurée automatiquement en cas d’échec de mise à jour des services par défaut.
3. Les services par défaut présents dans le [manifeste de l’application](service-fabric-application-and-service-manifests.md) précédent et non dans la nouvelle version sont supprimés. **Notez qu’il n’est pas possible de rétablir les services par défaut supprimés.**

Lorsqu’une mise à niveau d’application est restaurée, les services par défaut sont rétablis à l’état précédant le début de la mise à niveau. Mais les services supprimés ne peuvent plus être créés.

## <a name="application-upgrade-flowchart"></a>Organigramme de la mise à niveau d'application
L’organigramme suivant ce paragraphe peut vous aider à comprendre le processus de mise à niveau d’une application Service Fabric. En particulier, le flux indique dans quelle mesure les délais, notamment *HealthCheckStableDuration*, *HealthCheckRetryTimeout* et *UpgradeHealthCheckInterval*, déterminent l’échec ou la réussite de la mise à niveau dans un domaine de mise à jour donné.

![Processus de mise à niveau d'une application Service Fabric][image]

## <a name="next-steps"></a>Étapes suivantes
[mise à niveau de votre application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

[mise à niveau de votre application à l’aide de PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) vous guide à travers une mise à niveau de l’application à l’aide de PowerShell.

Contrôlez les mises à niveau de votre application à l'aide des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Rendez les mises à niveau de votre application compatibles en apprenant à utilisez la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en consultant les [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résolvez les problèmes courants de mise à niveau de l’application en vous reportant aux étapes de [Résolution des problèmes de mise à niveau des applications](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
