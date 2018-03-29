---
title: Introduction aux Collections fiables dans les services avec état d’Azure Service Fabric | Microsoft Docs
description: Les services avec état de Service Fabric fournissent des collections fiables qui vous permettent d’écrire des applications cloud hautement disponibles, évolutives et à faible latence.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: c6425f7642d9dbecafc6779184a2a5b0cc1a2cab
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduction aux Collections fiables dans les services avec état d’Azure Service Fabric
Les Collections fiables vous permettent d’écrire des applications cloud hautement disponibles, évolutives et à faible latence comme si vous écriviez des applications informatiques uniques. Les classes dans l’espace de noms **Microsoft.ServiceFabric.Data.Collections** fournissent un ensemble de collections qui rendent automatiquement votre état hautement disponible. Les développeurs doivent programmer uniquement les API de Collection fiable et laisser les Collections fiables gérer l’état répliqué et local.

La principale différence entre les Collections fiables et d'autres technologies de haute disponibilité (comme Redis, le service Table Azure et le service File d'attente Azure) est que l'état est conservé localement dans l'instance de service tout en étant également rendu hautement disponible. Cela signifie que :

* Toutes les lectures sont locales, ce qui entraîne des lectures à faible latence et à débit élevé.
* Toutes les écritures induisent un nombre minimal d’entrées et sorties réseau, ce qui entraîne des écritures à latence faible et à débit élevé.

![Image de l'évolution des collections.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Les Collections fiables peuvent être considérées comme l’évolution naturelle des classes **System.Collections** : un nouveau jeu de collections qui sont conçues pour les applications cloud et les applications pour plusieurs ordinateurs sans accroître la complexité pour les développeurs. En tant que telles, les Collections fiables sont :

* Répliquées : les modifications d'état sont répliquées pour une haute disponibilité.
* Persistantes : les données sont conservées sur le disque pour la durabilité contre les pannes à grande échelle (par exemple, une panne de courant dans un centre de données).
* Asynchrones : les API sont asynchrones afin de s’assurer que les threads ne sont pas bloqués en cas d’entrées/sorties.
* Transactionnelles : les API utilisent l’abstraction de transactions pour vous permettre de gérer facilement plusieurs Collections fiables au sein d’un service.

Les Collections fiables fournissent des garanties de forte cohérence instantanée afin de faciliter le raisonnement sur l'état de l'application.
La forte cohérence est obtenue en s’assurant que la transaction n’est validée comme terminée que lorsque l’intégralité de la transaction a été enregistrée dans un quorum majoritaire de réplicas, y compris le réplica principal.
Pour obtenir une cohérence plus faible, les applications peuvent accuser réception au client/demandeur avant la validation asynchrone.

Les API de Collections fiables sont une évolution des API de collections simultanées (trouvées dans l’espace de noms **System.Collections.Concurrent** ) :

* Asynchrones : renvoie une tâche, car contrairement aux collections simultanées, les opérations sont répliquées et conservées.
* Aucun paramètre de sortie : utilise `ConditionalValue<T>` pour renvoyer un paramètre booléen et une valeur au lieu de paramètres. `ConditionalValue<T>` est similaire à `Nullable<T>`, mais ne nécessite ne pas que T soit une structure.
* Transactions : utilise un objet de transaction pour permettre à l'utilisateur de regrouper des actions sur plusieurs Collections fiables dans une transaction.

Actuellement, **Microsoft.ServiceFabric.Data.Collections** contient trois collections :

* [Dictionnaire fiable](https://msdn.microsoft.com/library/azure/dn971511.aspx): représente une collection répliquée, transactionnelle et asynchrone de paires clé/valeur. Semblables à celles de **ConcurrentDictionary**, la clé et la valeur peuvent être de tout type.
* [File d’attente fiable](https://msdn.microsoft.com/library/azure/dn971527.aspx): représente une file d’attente FIFO stricte, répliquée, transactionnelle et asynchrone. Semblable à celle de **ConcurrentQueue**, la valeur peut être de tout type.
* [File d’attente simultanée fiable](service-fabric-reliable-services-reliable-concurrent-queue.md) : représente une file d’attente de classement de meilleur effort répliquée, transactionnelle et asynchrone, pour un débit élevé. Semblable à celle de **ConcurrentQueue**, la valeur peut être de tout type.

## <a name="next-steps"></a>Étapes suivantes
* [Instructions et recommandations relatives aux collections fiables](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Utilisation des collections fiables](service-fabric-work-with-reliable-collections.md)
* [Transactions et verrous](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gestion des données
  * [Sauvegarde et restauration](service-fabric-reliable-services-backup-restore.md)
  * [Notifications](service-fabric-reliable-services-notifications.md)
  * [Sérialisation de Collection fiable](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Sérialisation et mise à niveau](service-fabric-application-upgrade-data-serialization.md)
  * [Configuration du Gestionnaire d’état fiable](service-fabric-reliable-services-configuration.md)
* Autres
  * [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Référence du développeur pour les Collections fiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
