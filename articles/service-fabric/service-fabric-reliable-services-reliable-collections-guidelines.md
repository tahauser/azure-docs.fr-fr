---
title: Instructions et recommandations pour les collections fiables dans Azure Service Fabric | Microsoft Docs
description: Instructions et recommandations relatives à l’utilisation de collections fiables Fabric Service
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
ms.date: 12/10/2017
ms.author: mcoskun
ms.openlocfilehash: 27ea71bcc378100e613a8edd1c57a93f3c9ed925
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Instructions et recommandations pour les collections fiables dans Azure Service Fabric
Cette section fournit des instructions pour l’utilisation du gestionnaire d’état fiable et des collections fiables. L’objectif consiste à aider les utilisateurs à éviter les pièges courants.

Les directives sont organisées comme de simples recommandations avec les termes *Faire*, *Envisager*, *Éviter* et *Ne pas faire* en préfixe.

* Ne modifiez pas un objet de type personnalisé renvoyé par les opérations de lecture (par exemple, `TryPeekAsync` ou `TryGetValueAsync`). Les Collections fiables, comme les Collections simultanées, renvoient une référence aux objets et non une copie.
* Exécutez une copie complète de l’objet renvoyé de type personnalisé avant de le modifier. Étant donné que les structures et les types intégrés sont de passage par valeur, vous n’avez pas besoin d’y effectuer une copie complète, sauf s’ils contiennent des champs ou des propriétés saisis par référence que vous souhaitez modifier.
* N’utilisez pas `TimeSpan.MaxValue` pour les délais d’attente. Les délais d’expiration doivent être utilisés pour détecter des blocages.
* N’utilisez pas une transaction une fois qu’elle a été validée, abandonnée ou supprimée.
* N’utilisez pas une énumération en dehors de l’étendue de transaction dans laquelle elle a été créée.
* Ne créez pas une transaction au sein de l’instruction `using` d’une autre transaction, car cela peut provoquer des blocages.
* Assurez-vous que votre implémentation de `IComparable<TKey>` est correcte. Le système dépend de `IComparable<TKey>` pour la fusion des points de contrôle et des lignes.
* N’utilisez pas un verrou de mise à jour lors de la lecture d’un élément avec l’intention de le mettre à jour pour empêcher une certaine classe de blocages.
* Envisagez de conserver le nombre de Reliable Collections par partition sur un nombre inférieur à 1 000. Privilégiez les Reliable Collections avec plus d’éléments par rapport à plus de Reliable Collections avec moins d’éléments.
* Envisagez de conserver vos éléments (par ex. TKey + TValue pour le Dictionnaire fiable) sous 80 Ko : plus le volume est petit, mieux c’est. Cela réduit la quantité d’utilisation du Large Object Heap, ainsi que les exigences d’E/S relatives au disque et au réseau. Souvent, cela permet de réduire la réplication de données en double lorsque seule une petite partie de la valeur est mise à jour. La manière la plus courante pour y parvenir dans le Dictionnaire fiable consiste à diviser vos lignes en plusieurs lignes.
* Envisagez d’utiliser la fonctionnalité de sauvegarde et de restauration pour bénéficier de la récupération d’urgence.
* Évitez de combiner des opérations à une seule entité et des opérations à plusieurs entités (par exemple `GetCountAsync`, `CreateEnumerableAsync`) dans la même transaction en raison des différents niveaux d’isolement.
* Gérez l’exception InvalidOperationException. Les transactions des utilisateurs peuvent être annulées par le système pour diverses raisons. Par exemple, lorsque le Gestionnaire d’état fiable abandonne le rôle Principal ou qu’une transaction longue bloque la troncature du journal des transactions. Dans ce cas, l’utilisateur peut recevoir l’exception InvalidOperationException, indiquant que sa transaction a déjà été terminée. Dans l’hypothèse où l’arrêt de la transaction n’était pas demandé par l’utilisateur, la meilleure façon de gérer cette exception consiste à supprimer la transaction, vérifier si le jeton d’annulation a été signalé (ou si le rôle du réplica a été modifié) et, si ce n’est pas le cas, créer une nouvelle transaction, puis réessayer.  

Voici quelques points à retenir :

* Le délai d’expiration par défaut est de quatre secondes pour toutes les API de Collections fiables. La plupart des utilisateurs souhaiteront utiliser le délai d’attente par défaut.
* Le jeton d'annulation par défaut est `CancellationToken.None` dans toutes les API de Collections fiables.
* Le paramètre de type de clé (*TKey*) pour un Dictionnaire fiable doit implémenter correctement `GetHashCode()` et `Equals()`. Les clés doivent être immuables.
* Pour obtenir un haut niveau de disponibilité pour les Collections fiables, chaque service doit avoir au moins une taille de jeu de réplicas cible minimum égale à 3.
* Les opérations de lecture sur le secondaire peuvent lire des versions qui ne sont pas validées dans le quorum.
  Cela signifie qu’une version des données lue à partir d’un seul secondaire peut présenter une progression erronée.
  Les lectures à partir du principal sont toujours stables : la progression n’est jamais erronée.

### <a name="next-steps"></a>Étapes suivantes
* [Utilisation des collections fiables](service-fabric-work-with-reliable-collections.md)
* [Transactions et verrous](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* [Gestionnaire d’état fiable et éléments internes de collections](service-fabric-reliable-services-reliable-collections-internals.md)
* Gestion des données
  * [Sauvegarde et restauration](service-fabric-reliable-services-backup-restore.md)
  * [Notifications](service-fabric-reliable-services-notifications.md)
  * [Sérialisation et mise à niveau](service-fabric-application-upgrade-data-serialization.md)
  * [Configuration du Gestionnaire d’état fiable](service-fabric-reliable-services-configuration.md)
* Autres
  * [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Référence du développeur pour les Collections fiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
