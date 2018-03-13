---
title: Questions courantes sur Microsoft Azure Service Fabric | Microsoft Docs
description: "Questions fréquentes sur Service Fabric et leurs réponses"
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: facbb980f57b4e70c34b238a8b8fbd988cb20d57
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="commonly-asked-service-fabric-questions"></a>Questions fréquentes sur Service Fabric

Les utilisateurs posent fréquemment des questions sur l’utilisation et les fonctionnalités de Service Fabric. Ce document regroupe un grand nombre de ces questions fréquentes et leurs réponses.

## <a name="cluster-setup-and-management"></a>Création et gestion de clusters

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Puis-je créer un cluster qui englobe plusieurs régions Azure ou mes propres centres de données ?

Oui. 

La technologie de clustering Service Fabric permet de combiner des machines qui s’exécutent partout dans le monde, tant qu’elles sont connectées entre elles par un réseau. Toutefois, la création et l’exécution d’un tel cluster peuvent être compliquées.

Si vous êtes intéressé par ce scénario, nous vous encourageons à nous contacter via la [Liste de problèmes Service Fabric sur Github](https://github.com/azure/service-fabric-issues) ou via votre technicien de support, afin d’obtenir une aide supplémentaire. L’équipe Service Fabric travaille à fournir des instructions et des recommandations claires pour ce scénario. 

Voici quelques points importants à prendre en compte : 

1. À l’heure actuelle, la ressource de cluster Service Fabric dans Azure est régionale, comme les groupes de machines virtuelles identiques sur lesquels le cluster est basé. De fait, en cas de défaillance régionale, vous risquez de perdre la possibilité de gérer le cluster via Azure Resource Manager ou le portail Azure. Cela peut se produire même si le cluster continue de s’exécuter et que vous pouvez interagir directement avec lui. En outre, Azure ne permet pas à un même réseau virtuel d’être utilisé par plusieurs régions. Par conséquent, un cluster Azure à plusieurs régions nécessite soit des [adresses IP publiques pour chaque machine virtuelle dans VM Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine), soit des [passerelles VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Toutes ces options de réseau ont un impact différent sur les coûts, les performances, et à un certain degré, sur la conception de l’application. Il est donc nécessaire d’analyser et de planifier avec soin avant de configurer un tel environnement.
2. La maintenance, la gestion et la surveillance de ces machines peuvent devenir complexes, en particulier en cas de répartition sur plusieurs _types_ d’environnements, par exemple, entre différents fournisseurs cloud, ou entre des ressources locales et des ressources Azure. Vérifiez bien que les mises à niveau, la surveillance, la gestion et les diagnostics ont été compris à la fois pour le cluster et les applications avant d’exécuter les charges de travail de production dans un tel environnement. Si vous avez l’habitude de résoudre ces problèmes dans Azure ou dans vos propres centres de données, il est probable que vous puissiez appliquer ces mêmes solutions lors de la création ou de l’exécution de votre cluster Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Les nœuds Service Fabric reçoivent-ils automatiquement les mises à jour du système d’exploitation ?

Pas encore, mais il s’agit d’une demande courante que nous prévoyons de mettre en œuvre.

En attendant, nous avons [fourni une application](service-fabric-patch-orchestration-application.md) permettant aux systèmes d’exploitation situés sous vos nœuds Service Fabric de rester à jour et d’obtenir les derniers correctifs.

Avec les mises à jour du système d’exploitation, le défi réside dans le fait qu’elles nécessitent généralement un redémarrage de l’ordinateur, ce qui entraîne une indisponibilité temporaire. En soi, ce n’est pas un problème, car Service Fabric redirige automatiquement le trafic de ces services vers d’autres nœuds. Toutefois, si les mises à jour du système d’exploitation ne sont pas coordonnées dans le cluster, plusieurs nœuds risquent d’être indisponibles en même temps. Ces redémarrages simultanés peuvent entraîner l’indisponibilité complète d’un service ou, au moins, d’une partition spécifique (pour un service avec état).

À l’avenir, nous prévoyons de prendre en charge une stratégie de mise à jour du système d’exploitation totalement automatisée et coordonnée entre les domaines de mise à jour, ce qui garantit une disponibilité continue malgré les redémarrages et autres défaillances inattendues.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Puis-je utiliser de grands groupes de machines virtuelles identiques dans mon cluster Service Fabric ? 

**Réponse courte** : Non. 

**Réponse longue** : Bien que les grands groupes de machines virtuelles identiques puissent évoluer jusqu’à 1 000 instances de machines virtuelles, ils utilisent pour cela des groupes de placement. Les domaines d’erreur (FD) et les domaines de mise à niveau (UD) ne sont cohérents qu’au sein d’un groupe de placement. Service Fabric utilise des domaines d’erreur et des domaines de mise à niveau pour prendre des décisions de placement de vos instances/réplicas de service. Étant donné que les domaines d’erreur et de mise à niveau ne sont comparables qu’au sein d’un groupe de placement, Service Fabric ne peut pas les utiliser. Par exemple, si VM1 dans PG1 a une topologie de FD = 0 et VM9 dans PG2 a une topologie de FD = 4, cela ne signifie pas que VM1 et VM2 se trouvent sur deux racks matériels différents. Par conséquent, Service Fabric ne peut pas dans ce cas utiliser les valeurs FD pour prendre des décisions de placement.

Il y a actuellement d’autres problèmes avec les groupes de machines virtuelles identiques volumineux, notamment l’absence de prise en charge de l’équilibrage de charge de niveau 4. Pour plus d’informations, consultez la page [Grands groupes identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Quelle est la taille minimale d’un cluster Service Fabric ? Pourquoi ne peut-il pas être plus petit ?

La taille minimale prise en charge pour un cluster Service Fabric exécutant des charges de travail de production est de cinq nœuds. Pour les scénarios de développement/test, nous prenons en charge des clusters à trois nœuds.

Ces valeurs minimales existent parce que le cluster Service Fabric exécute un ensemble de services système avec état, dont le service d’affectation de noms et Failover Manager. Ces services, qui suivent les services déployés sur le cluster ainsi que leur emplacement d’hébergement actuel, requièrent une cohérence forte. Cette cohérence forte, quant à elle, dépend de la capacité d’atteindre un *quorum* pour une mise à jour donnée de l’état de ces services, ce quorum représentant une majorité stricte des réplicas (N/2 + 1) pour un service donné.

Dans ce cadre, examinons certaines configurations de cluster possibles :

**Un nœud** : cette option n’assure pas une haute disponibilité, car la perte du nœud pour une raison quelconque se traduit par la perte du cluster.

**Deux nœuds** : le quorum d’un service déployé sur deux nœuds (N = 2) est 2 (2/2 + 1 = 2). En cas de perte d’un réplica, il est impossible d’obtenir un quorum. Comme la mise à niveau d’un serveur requiert la mise hors ligne d’un réplica, cette configuration n’a aucune utilité.

**Trois nœuds** : avec trois nœuds (N = 3), la condition requise pour créer un quorum reste de deux nœuds (3/2 + 1 = 2). Cela signifie que vous pouvez perdre un nœud et conserver le quorum.

La configuration de cluster à trois nœuds est prise en charge pour le développement/test, car vous pouvez en toute sécurité effectuer des mises à niveau et faire face aux défaillances d’un nœud, tant que ces deux événements ne sont pas simultanés. Pour les charges de travail de production, vous devez être résilient face à une telle défaillance simultanée. Cinq nœuds sont donc nécessaires.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Puis-je désactiver mon cluster la nuit et le week-end pour réduire les coûts ?

En général, non. Service Fabric stocke l’état sur des disques éphémères locaux. En d’autres termes, si la machine virtuelle est déplacée vers un hôte différent, les données n’effectuent pas le déplacement. Normalement, ce n’est pas un problème car le nouveau nœud est mis à jour par d’autres nœuds. Toutefois, si vous arrêtez tous les nœuds et que vous les redémarrez ultérieurement, il est fort probable que la plupart des nœuds démarrent sur de nouveaux hôtes et empêchent la restauration du système.

Si vous souhaitez créer des clusters pour tester votre application avant de la déployer, nous vous recommandons de les créer dynamiquement dans le cadre de votre [pipeline d’intégration continue/de déploiement continu](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Comment mettre à niveau mon système d’exploitation (par exemple de Windows Server 2012 vers Windows Server 2016) ?

Alors que nous travaillons sur une expérience améliorée, vous êtes aujourd’hui chargé de la mise à niveau. Vous devez mettre à niveau l’image du système d’exploitation sur les machines virtuelles du cluster, une machine virtuelle à la fois. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Puis-je chiffrer des disques de données associés dans un type de nœud de cluster (groupe de machines virtuelles identiques) ?
Oui.  Pour plus d’informations, consultez [Créer un cluster avec des disques de données associés](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [Chiffrer des disques (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) et [Chiffrer des disques (CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

## <a name="container-support"></a>Support pour les conteneurs

### <a name="why-are-my-containers-that-are-deployed-to-sf-unable-to-resolve-dns-addresses"></a>Pourquoi mes conteneurs déployés sur SF ne parviennent-ils pas à résoudre les adresses DNS ?

Ce problème a été signalé sur les clusters version 5.6.204.9494 

**Solution de contournement** : Suivez les instructions de [ce document](service-fabric-dnsservice.md) pour activer le service DNS de Service Fabric dans votre cluster.

**Correction** : Mettez le cluster à niveau vers une version prise en charge supérieure à 5.6.204.9494, si une telle version est disponible. Si votre cluster est configuré pour se mettre à niveau automatiquement, le cluster se mettra automatiquement à niveau vers une version pour laquelle ce problème est résolu.

  
## <a name="application-design"></a>Conception des applications

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Quel est le meilleur moyen d’interroger des données sur plusieurs partitions d’une collection fiable ?

Les collections fiables sont généralement [partitionnées](service-fabric-concepts-partitioning.md) pour permettre l’extensibilité et augmenter les performances ainsi que le débit. Cela signifie que l’état d’un service peut être réparti entre des dizaines voire des centaines d’ordinateurs. Pour effectuer des opérations sur ce jeu de données complet, plusieurs possibilités s’offrent à vous :

- Créer un service qui interroge toutes les partitions d’un autre service pour extraire les données requises.
- Créer un service capable de recevoir des données provenant de toutes les partitions d’un autre service.
- Envoyer régulièrement des données de chaque service à un magasin externe. Cette approche ne convient que si les requêtes que vous exécutez ne font pas partie de votre logique d’entreprise.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Quel est le meilleur moyen d’interroger les données entre mes différents acteurs ?

Les acteurs sont conçus comme des unités indépendantes d’état et de calcul. Il n’est pas recommandé d’effectuer des requêtes volumineuses sur l’état des acteurs pendant l’exécution. Si vous devez exécuter une requête sur l’ensemble des états d’acteur, vous devez envisager de :

- Remplacer vos services d’acteur par des services fiables avec état, comme le nombre de requêtes réseau, pour collecter toutes les données, depuis le nombre d’acteurs jusqu’au nombre de partitions de votre service.
- Concevoir vos acteurs pour qu’ils envoient régulièrement leur état à un magasin externe afin de faciliter les requêtes. Comme auparavant, cette approche n’est viable que si les requêtes que vous exécutez ne sont pas obligatoires pour votre comportement d’exécution.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Quelle quantité de données puis-je stocker dans une collection fiable ?

Les services fiables sont généralement partitionnés. La quantité que vous pouvez stocker n’est donc limitée que par le nombre d’ordinateurs dans votre cluster et par la quantité de mémoire disponible sur ces ordinateurs.

Par exemple, supposons que vous ayez une collection fiable dans un service de 100 partitions et 3 réplicas, la taille moyenne des objets stockés est de 1 Ko. Maintenant, supposons que vous disposiez d’un cluster de 10 ordinateurs avec 16 Go de mémoire par ordinateur. Par souci de simplicité et par prudence, supposons que le système d’exploitation, les services système, le runtime Service Fabric et vos services consomment 6 Go, ce qui laisse 10 Go disponibles par ordinateur soit 100 Go pour le cluster.

Sachant que chaque objet doit être stocké trois fois (une copie principale et deux réplicas), vous aurez suffisamment de mémoire pour environ 35 millions d’objets dans votre collection lorsqu’elle fonctionnera à pleine capacité. Toutefois, nous vous recommandons d’assurer une résilience à la perte simultanée d’un domaine de défaillance et d’un domaine de mise à niveau, ce qui représente environ 1/3 de la capacité et réduit le nombre à environ 23 millions.

Notez que ce calcul suppose également :

- Que la répartition des données entre les partitions est à peu près uniforme ou que vous envoyez des métriques de charge à Cluster Resource Manager. Par défaut, Service Fabric équilibre la charge en fonction du nombre de réplicas. Dans l’exemple précédent, 10 réplicas principaux et 20 réplicas secondaires seraient placés sur chaque nœud du cluster. Cela fonctionne bien pour la charge répartie équitablement entre les partitions. Si la charge n’est pas équilibrée, vous devez la signaler afin que le Resource Manager puisse regrouper des réplicas plus petits et autoriser des réplicas plus volumineux à consommer davantage de mémoire sur un nœud individuel.

- Que le service fiable en question est le seul à stocker l’état dans le cluster. Comme vous pouvez déployer plusieurs services dans un cluster, vous devez être conscient que les ressources de chacun doivent exécuter et gérer son état.

- Que le cluster lui-même pas n’augmente pas ou ne diminue pas en taille. Si vous ajoutez d’autres ordinateurs, Service Fabric rééquilibre vos réplicas en fonction des capacités supplémentaires jusqu’à ce que le nombre d’ordinateurs dépasse le nombre de partitions dans votre service, car un réplica ne peut pas englober plusieurs ordinateurs. En revanche, si vous réduisez la taille du cluster en supprimant des ordinateurs, vos réplicas sont davantage compressés et ont une capacité moindre.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Quelle quantité de données puis-je stocker dans un acteur ?

Comme avec les services fiables, la quantité de données que vous pouvez stocker dans un service d’acteur n’est limitée que par l’espace disque total et la mémoire disponible sur les nœuds de votre cluster. Toutefois, les acteurs sont plus efficaces lorsqu’ils sont utilisés pour encapsuler une petite quantité d’état et de logique métier associée. En règle générale, un acteur doit avoir l’état qui est mesuré en kilo-octets.

## <a name="other-questions"></a>Autres questions

### <a name="how-does-service-fabric-relate-to-containers"></a>Quel est le rapport entre Service Fabric et les conteneurs ?

Les conteneurs constituent un moyen simple d’encapsuler les services et leurs dépendances pour qu’ils s’exécutent de manière cohérente dans tous les environnements et puissent fonctionner de manière isolée sur un seul ordinateur. Service Fabric vous permet de déployer et gérer des services, notamment des [services encapsulés dans un conteneur](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Prévoyez-vous de diffuser le code de Service Fabric ?

Nous avons l’intention de diffuser le code des frameworks Reliable Services et Reliable Actors sur GitHub et d’accepter les contributions de la communauté à ces projets. Consultez le [blog Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) pour en savoir plus.

Pour l’instant, il n’est pas prévu de diffuser le code du runtime Service Fabric.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les principaux concepts et les meilleures pratiques de Service Fabric](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)
