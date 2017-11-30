---
title: "Surveillance au niveau de plateforme d’Azure Service Fabric | Microsoft Docs"
description: "Découvrez les événements et journaux au niveau de la plateforme utilisés pour surveiller et diagnostiquer les clusters Azure Service Fabric."
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
ms.date: 11/20/2017
ms.author: dekapur
ms.openlocfilehash: 1277969d820c6348392d07e2dc5f35b52377fd7e
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="platform-level-event-and-log-generation"></a>Événement au niveau de la plateforme et génération de journal

## <a name="monitoring-the-cluster"></a>Surveillance du cluster

Il est important d’effectuer une surveillance au niveau de la plateforme pour déterminer si votre matériel et votre cluster se comportent comme prévu. Même si Service Fabric peut garantir l’exécution continue d’une application pendant les pannes de matériel, vous devez toujours déterminer si une erreur se produit dans l’application ou dans l’infrastructure sous-jacente. Vous devez surveiller également vos clusters pour mieux planifier la capacité et aider à prendre les décisions concernant l’ajout ou la suppression de matériel.

Service Fabric fournit les canaux de journal suivants prêts à l’emploi :
* Canal opérationnel : opérations de haut niveau effectuées par Service Fabric et le cluster. Cela comprend les événements pour un nœud mis en ligne, une nouvelle application déployée, l’annulation d’une mise à niveau, etc.
* Canal opérationnel - détaillé : rapports d’intégrité et décisions d’équilibrage de charge
* Canal de données et de messagerie : événements et journaux critiques générés dans la messagerie (pour le moment le proxy inverse uniquement) et chemin de données (modèles de services fiables)
* Canal de données et de messagerie - détaillée : canal détaillé qui contient tous les journaux non critiques à partir de données et de messagerie dans le cluster (ce canal a un volume très élevé d’événements)   

En plus de ceux-ci, deux canaux EventSource structurés sont fournis, ainsi que des journaux que nous collectons à des fins de support.
* [Événements Reliable Services](service-fabric-reliable-services-diagnostics.md) : événements spécifiques au modèle de programmation
* [Événements Reliable Actors](service-fabric-reliable-actors-diagnostics.md) : compteurs de performances et événements spécifiques au modèle de programmation
* Journaux de support : journaux générés par Service Fabric uniquement pour être utilisés de notre côté lorsque nous vous fournissons le support technique

Ces différents canaux couvrent la majeure partie de la journalisation recommandée au niveau de la plateforme. Pour améliorer la journalisation au niveau de la plateforme, essayez de mieux comprendre le modèle d’intégrité et d’ajouter des rapports d’intégrités personnalisés, ainsi que d’ajouter des **compteurs de performances** pour pouvoir comprendre en temps réel l’impact de vos services et applications sur le cluster.

### <a name="azure-service-fabric-health-and-load-reporting"></a>Rapports d’intégrité et de charge Azure Service Fabric

Service Fabric présente son propre modèle de contrôle d’intégrité, qui est détaillé dans les articles suivants :
- [Présentation du contrôle d’intégrité de Service Fabric](service-fabric-health-introduction.md)
- [Signaler et contrôler l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Ajout de rapports d’intégrité Service Fabric personnalisés](service-fabric-report-health.md)
- [Affichage rapports d’intégrité de Service Fabric](service-fabric-view-entities-aggregated-health.md)

Le contrôle d’intégrité est essentiel à plusieurs aspects de l’exploitation d’un service. Le contrôle d’intégrité est particulièrement important lorsque Service Fabric effectue la mise à niveau d’une application nommée. À chaque fois qu’un domaine de mise à niveau du service est mis à niveau et devient accessible à vos clients, il doit passer des contrôles d’intégrité avec succès avant que le déploiement puisse passer au domaine de mise à niveau suivant. S’il est impossible d’obtenir un état d’intégrité correct, le déploiement est restauré afin de laisser l’application dans un état correct connu. Bien que certains clients puissent être affectés avant la restauration des services, la plupart des clients ne rencontreront aucun problème. En outre, la résolution s’effectue assez rapidement, sans nécessiter d’intervention humaine. Plus votre code contient de contrôles d’intégrité, plus votre service est résilient face aux problèmes de déploiement.

L’intégrité du service inclut un autre aspect : le rapport de mesures à partir du service. Les mesures sont importantes dans Service Fabric, car elles permettent d’équilibrer l’utilisation des ressources. Les mesures peuvent aussi servir d’indicateur de l’intégrité du système. Par exemple, vous pouvez avoir une application qui comporte de nombreux services, chaque instance envoyant des rapports sur une mesure de demandes par seconde (RPS). Si un service utilise plus de ressources qu’un autre, Service Fabric déplace les instances de service autour du cluster afin de maintenir une utilisation homogène des ressources. Pour une explication plus détaillée du fonctionnement de l’utilisation des ressources, consultez [Gestion de la consommation des ressources et des charges dans Service Fabric à l’aide de mesures](service-fabric-cluster-resource-manager-metrics.md).

Les mesures peuvent également vous renseigner sur les performances de votre service. Au fil du temps, vous pouvez utiliser des mesures pour vérifier que le service fonctionne selon les paramètres prévus. Par exemple, si les tendances indiquent que le nombre moyen de demandes par seconde (RPS) s’élève à 1 000 le lundi à 9 h 00, vous pouvez configurer un rapport d’intégrité qui vous alerte lorsque le RPS est inférieur à 500 ou supérieur à 1 500. Tout peut se passer parfaitement bien, mais il peut être intéressant de vérifier la qualité de l’expérience dont bénéficient vos clients. Votre service offre la possibilité de définir un ensemble de mesures pouvant faire l’objet de rapports dans le cadre de contrôles d’intégrité, mais qui n’affectent pas l’équilibre des ressources du cluster. Pour ce faire, définissez le poids de mesure sur zéro. Nous vous recommandons de démarrer toutes les mesures avec un poids égal à zéro et de ne pas augmenter cette valeur avant d’être certain de bien comprendre l’impact de la pondération des mesures sur l’équilibrage des ressources de votre cluster.

> [!TIP]
> N’utilisez pas un trop grand nombre de mesures pondérées. Il peut être difficile de comprendre pourquoi les instances de service sont déplacées à des fins d’équilibrage. Quelques mesures peuvent en dire long !

Toute information qui peut être un indicateur de l’intégrité et des performances de votre application est appropriée pour les rapports de mesure et d’intégrité. Un compteur de performances du processeur peut vous renseigner sur l’utilisation de votre nœud, mais il ne vous permet pas de savoir si un service particulier est intègre, car plusieurs services peuvent s’exécuter sur un seul nœud. Cependant, des mesures telles que les demandes par seconde (RPS), les éléments traités et la latence des demandes peuvent toutes être un indicateur de l’intégrité d’un service spécifique.

Pour obtenir des rapports d’intégrité, utilisez un code similaire à ceci :

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Pour obtenir des rapports sur une mesure, utilisez un code similaire à ceci :

  ```csharp
    this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

### <a name="service-fabric-support-logs"></a>Journaux de support Service Fabric

Si vous devez contacter le support technique Microsoft pour obtenir de l’aide sur votre cluster Azure Service Fabric, les journaux de support sont presque toujours requis. Si votre cluster est hébergé dans Azure, ces journaux sont automatiquement configurés et collectés lors de la création du cluster. Les journaux sont stockés dans un compte de stockage dédié dans le groupe de ressources de votre cluster. Le compte de stockage ne présente pas de nom fixe, mais le compte recense des conteneurs d’objets blob et des tables dont le nom commence par *fabric*. Pour plus d’informations sur la configuration de la collecte de journaux pour un cluster autonome, consultez [Créer et gérer un cluster Azure Service Fabric autonome](service-fabric-cluster-creation-for-windows-server.md) et [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md). Pour les instances Service Fabric autonomes, les journaux doivent être envoyés vers un partage de fichiers local. Vous **devez** communiquer ces journaux à l’équipe de support, qui ne diffuse en aucun cas ces documents à d’autres fins.

## <a name="enabling-diagnostics-for-a-cluster"></a>Activation des diagnostics pour un cluster

Pour tirer parti de ces journaux, il est vivement recommandé d’activer « Diagnostics » lors de la création du cluster. Si vous activez les diagnostics lorsque le cluster est déployé, Windows Azure Diagnostics est en mesure de confirmer les canaux Operational, Reliable Services et Reliable Actors, et de stocker les données, comme expliqué dans [Agréger des événements avec Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).

Comme indiqué ci-dessus, il existe également un champ facultatif pour ajouter une clé d’instrumentation Application Insights (AI). Si vous choisissez d’utiliser AI pour des analyses d’événement (en savoir plus dans [Événements d’analyse avec Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)), insérez la clé d’instrumentation de ressource AppInsights (GUID) ici.


Si vous souhaitez déployer des conteneurs dans votre cluster, autorisez WAD à récupérer les statistiques de docker en ajoutant ce qui suit à votre « WadCfg > DiagnosticMonitorConfiguration » :

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},

```

## <a name="measuring-performance"></a>Mesure des performances

Mesurer les performances de votre cluster vous permet de comprendre comment il parvient à gérer la charge et à orienter les décisions concernant la mise à l’échelle de votre cluster (en savoir plus sur la mise à l’échelle d’un cluster [sur Azure](service-fabric-cluster-scale-up-down.md) ou [local](service-fabric-cluster-windows-server-add-remove-nodes.md)). Les données de performances sont également utiles quand elles sont comparées aux actions que vous-même, ou vos applications et services, avez prises, lorsque vous analysez plus tard des journaux. 

Pour obtenir la liste des compteurs de performances à collecter lors de l’utilisation de Service Fabric, consultez l’article [Compteurs de performances dans Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Voici deux manières fréquentes de configurer la collecte des compteurs de performances pour votre cluster :

* À l’aide d’un agent : il s’agit du meilleur moyen pour collecter des performances à partir d’une machine, dans la mesure où les agents ont généralement une liste des mesures de performances possibles qui peuvent être collectées et le choix ou la modification des mesures que vous voulez collecter sont des processus relativement simples. Lisez les articles sur la [configuration d’OMS pour Service Fabric](service-fabric-diagnostics-event-analysis-oms.md) et la [configuration de l’agent Windows OMS](../log-analytics/log-analytics-windows-agents.md) pour en savoir plus sur l’agent OMS, un agent de surveillance qui est en mesure de récupérer les données de performances des machines virtuelles de cluster et des conteneurs déployés.

* Configuration des diagnostics pour écrire les compteurs de performances dans une table : pour les clusters sur Azure, cela implique de modifier Azure Diagnostics pour récupérer les compteurs de performances appropriés à partir des machines virtuelles de votre cluster et de lui permettre de récupérer les statistiques de docker, si vous souhaitez déployer des conteneurs. Lisez l’article sur les [compteurs de performances dans WAD](service-fabric-diagnostics-event-aggregation-wad.md) dans Service Fabric pour configurer la collecte des compteurs de performances.

## <a name="next-steps"></a>Étapes suivantes

Vos journaux et événements doivent être regroupés avant de pouvoir être envoyés à n’importe quelle plateforme d’analyse. Pour mieux comprendre certaines des options recommandées, consultez les informations relatives à [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) et [WAD](service-fabric-diagnostics-event-aggregation-wad.md).
