---
title: "Éléments internes de collection fiable et de gestionnaire d’état fiable Azure Service Fabric | Microsoft Docs"
description: "Présentation approfondie sur la conception et les concepts de collection fiable dans Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Éléments internes de collection fiable et de gestionnaire d’état fiable Azure Service Fabric
Ce document examine en profondeur le Gestionnaire d’état fiable et les Collections fiables pour comprendre comment les principaux composants fonctionnent en arrière-plan.

> [!NOTE]
> Ce document est en évolution. Laissez des commentaires sur cet article pour nous faire des suggestions sur les sujets à explorer.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>Modèle de persistance local : journal et point de contrôle
Le gestionnaire d’état fiable et les Collections fiables suivent un modèle de persistance appelé Journal et Point de contrôle.
Dans ce modèle, chaque changement d’état est d’abord enregistré sur le disque, puis appliqué dans la mémoire.
L’état complet proprement dit n’est conservé qu’occasionnellement (également appelé Point de contrôle).
L'avantage fourni est le suivant : les deltas sont transformés en écritures séquentielles en mode ajouter uniquement sur disque pour améliorer les performances.

Pour mieux comprendre le modèle de journal et de point de contrôle, penchons-nous d’abord sur le scénario de disque infini.
Le gestionnaire d’état fiable enregistre chaque opération avant qu’elle ne soit répliquée.
La journalisation permet aux Collections fiables d’appliquer l’opération uniquement dans la mémoire.
Dans la mesure où les journaux sont conservés, même lorsque le réplica échoue et doit être redémarré, le Gestionnaire d’état fiable possède suffisamment d’informations dans son journal pour recréer toutes les opérations perdues par le réplica.
Étant donné que le disque est infini, les enregistrements du journal n’ont jamais besoin d’être supprimés, et la Collection fiable ne doit gérer que l’état en mémoire.

Maintenant, examinons le scénario de disque limité.
À mesure que les journaux se remplissent, le gestionnaire d’état fiable manquera d’espace disque.
Avant que cela n’arrive, le gestionnaire d’état fiable doit tronquer son journal pour accueillir les enregistrements plus récents.
Le gestionnaire d’état fiable demande aux Collections fiables de contrôler leur état en mémoire sur disque.
À ce stade, les Collections fiables conservent leur état en mémoire.
Une fois que les Collections fiables ont terminé leur contrôle, le Gestionnaire d'état fiable peut tronquer le journal pour libérer de l'espace disque.
Lorsque le réplica doit être redémarré, les Collections fiables récupèrent leur état contrôlé et le Gestionnaire d’état fiable récupère et lit tous les changements d’état qui se sont produits depuis le dernier point de contrôle.

Le point de contrôle améliore également les délais de récupération dans les cas courants. Le journal contient toutes les opérations qui se sont produites depuis le dernier point de contrôle.
Par conséquent, il peut inclure plusieurs versions d’un élément. Par exemple, plusieurs valeurs pour une ligne donnée dans le dictionnaire fiable.
En revanche, la collection fiable ne contrôle que la dernière version de chaque valeur pour une clé.

## <a name="next-steps"></a>Étapes suivantes
* [Transactions et verrous](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

