---
title: Tarification et facturation de Service Bus | Microsoft Docs
description: "Présentation de la structure de prix de Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 8ccb44b5009588c28bc79bb45e1a7640ead6c817
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-pricing-and-billing"></a>Tarification et facturation de Service Bus

Azure Service Bus est disponible en niveau Standard et [Premium](service-bus-premium-messaging.md). Vous pouvez choisir un niveau de service pour chaque espace de noms Service Bus que vous créez ; cette sélection s’applique à l’ensemble des entités créées dans cet espace de noms.

> [!NOTE]
> Pour obtenir des informations détaillées sur la tarification actuelle de Service Bus, consultez la [page de tarification Azure Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) et la [FAQ de Service Bus](service-bus-faq.md#pricing).
>
>

Service Bus utilise les 2 mesures suivantes pour les files d’attente et les rubriques/abonnements :

1. **Opérations de messagerie**: définies en tant qu’appels API par rapport aux points de terminaison du service de file d’attente ou de rubrique/abonnement. Cette mesure remplace les messages envoyés ou reçus en tant qu’unité principale de l’utilisation facturable des files d’attente et des rubriques/abonnements.
2. **Connexions réparties**: définies comme le nombre maximal de connexions persistantes ouvertes par rapport aux files d’attente, rubriques ou abonnements pendant une période d’échantillonnage d’une heure donnée. Cette mesure s’applique uniquement au niveau Standard, dans lequel vous pouvez ouvrir des connexions supplémentaires (auparavant, les connexions étaient limitées à 100 par file d’attente/rubrique/abonnement) pour des frais minimaux par connexion.

Le niveau **standard** présente une tarification progressive pour les opérations exécutées avec les files d’attente et les rubriques/abonnements, ce qui entraîne des remises basées sur le volume de pouvant atteindre 80 %, pour les plus hauts niveaux d’utilisation. Il existe également des frais de base de niveau standard de 10 $ par mois, ce qui vous permet d'effectuer jusqu'à 12,5 millions d'opérations par mois sans frais supplémentaires.

Le niveau **premium** isole les ressources au niveau des couches processeur et mémoire, ce qui permet d'exécuter chaque charge de travail client de manière isolée. Ce conteneur de ressources est appelé *unité de messagerie*. Au moins une unité de messagerie est allouée à chaque espace de noms premium. Vous pouvez acheter une, deux ou quatre unités de messagerie pour chaque espace de noms Service Bus Premium. Une entité ou une charge de travail unique peut couvrir plusieurs unités de messagerie et le nombre d’unités de messagerie peut être modifié à volonté. En revanche, les frais qui vous sont facturés sont établis par période de 24 heures. Au final, les performances de votre solution Service Bus sont non seulement prévisibles et répétables, Au final, les performances de votre solution Service Bus sont non seulement prévisibles et répétables, mais aussi supérieures.

Les frais de base du niveau Standard sont facturés en une seule fois par mois et par abonnement Azure. Ainsi, une fois que vous avez créé un premier espace de noms Service Bus de niveau Standard, vous pouvez en créer autant que vous le souhaitez sous le même abonnement Azure, et ce sans frais de base supplémentaires.

Le tableau [Tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) résume les différences fonctionnelles entre les niveaux Standard et Premium.

## <a name="messaging-operations"></a>Opérations de messagerie

Les files d’attente et rubriques/abonnements sont facturés par « opération » et non par message. Une « opération » désigne un appel d’API sur un point de terminaison de service de file d’attente ou rubrique/d’abonnement. Cela inclut les opérations de gestion, d'envoi/réception et d'état de session.

| Type d'opération | Description |
| --- | --- |
| gestion |Création, lecture, mise à jour, suppression (CRUD) sur les files d’attente ou rubriques/abonnements. |
| Messagerie |Envoyer et recevoir des messages avec files d’attente ou rubriques/abonnements. |
| État de la session |Obtenir ou définir l’état d’une session sur une file d’attente ou rubrique/un abonnement. |

Pour plus d’informations sur les coûts, consultez les tarifs indiqués dans la page [Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="brokered-connections"></a>Connexions réparties

Les *connexions réparties* répondent aux modèles d’utilisation qui impliquent un grand nombre d’expéditeurs/de destinataires « connectés en permanence » aux files d’attente, aux rubriques ou aux abonnements. Les expéditeurs/destinataires connectés de façon permanente sont ceux qui se connectent à l'aide d'AMQP ou de HTTP avec un délai d'expiration de réception non nul (par exemple, l'interrogation longue HTTP). Les expéditeurs et les destinataires HTTP qui utilisent un délai d'expiration immédiat ne génèrent pas de connexions réparties.

Pour les quotas de connexion et les autres limites de service, consultez l’article [Quotas Service Bus](service-bus-quotas.md). Pour plus d’informations sur les connexions réparties, consultez la section [Questions fréquentes (FAQ)](#faq) plus loin dans cet article.

Le niveau Standard supprime la limite de connexion répartie par espace de noms et compte l’utilisation de connexions réparties de façon globale au sein de tout l’abonnement Azure. Pour plus d’informations, consultez le tableau [Connexions réparties](https://azure.microsoft.com/pricing/details/service-bus/).

> [!NOTE]
> 1 000 connexions réparties sont incluses avec le niveau de messagerie Standard (avec les frais de base) et peuvent être partagées entre toutes les files d’attente, toutes les rubriques et tous les abonnements au sein de l’abonnement Azure associé.
>
>

<br />

> [!NOTE]
> La facturation est basée sur le nombre maximal de connexions simultanées et est calculée au prorata toutes les heures basées sur 744 heures par mois.
>
>

### <a name="premium-tier"></a>Niveau premium

Les connexions réparties ne sont pas facturées au niveau premium.

## <a name="faq"></a>Forum Aux Questions

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Que sont les connexions réparties et combien me coûtent-elles ?

Une connexion répartie est définie comme suit :

1. Une connexion AMQP d’un client à une file d’attente ou une rubrique/un abonnement Service Bus.
2. Un appel HTTP pour recevoir le message d’une rubrique ou d’une file d’attente Service Bus dont le délai d’expiration de réception est supérieur à zéro.

Service Bus facture le nombre maximal de connexions simultanées réparties qui dépassent la quantité incluse (1 000 au niveau standard). Des pics sont mesurés toutes les heures, au prorata en divisant les valeurs par 744 heures par mois et en les ajoutant à la période de facturation mensuelle. La quantité incluse (1 000 connexions réparties par mois) est appliquée à la fin de la période de facturation par rapport à la somme des pics horaires calculés au prorata.

Par exemple : 

1. Chacun des 10 000 appareils se connecte via une connexion AMQP unique et reçoit des commandes à partir d’une rubrique Service Bus. Les appareils envoient des événements de télémétrie à un Event Hub. Si tous les appareils se connectent 12 heures par jour, les frais de connexion suivants s’appliquent (en plus des frais liés à une autre rubrique Service Bus) : 10 000 connexions * 12 heures * 31 jours / 744 = 5 000 connexions réparties. Après le volume mensuel de 1 000 connexions réparties, vous serez facturé pour les 4 000 connexions réparties, au tarif de 0,03 $ par connexion répartie, pour un total de 120 $.
2. 10 000 appareils reçoivent des messages d'une file d'attente Service Bus via HTTP, en spécifiant un délai d'expiration différent de zéro. Si tous les appareils se connectent 12 heures par jour, les frais de connexion suivants s’appliquent (en plus des autres frais liés à Service Bus) : 10 000 connexions de réception HTTP * 12 heures par jour * 31 jours / 744 heures = 5 000 connexions réparties.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Des frais de connexion répartie s’appliquent-ils aux files d’attente et rubriques/abonnements ?

Oui. Il n'y a aucun frais de connexion pour l'envoi d'événements à l'aide de HTTP, quel que soit le nombre de systèmes ou de périphériques d’envoi. La réception d'événements avec le protocole HTTP et à l'aide d'un délai d'expiration supérieur à zéro, parfois appelé « interrogation longue », génère des frais de connexion répartie. Les connexions AMQP génèrent des frais de connexion répartie peu importe si les connexions sont utilisées pour l'envoi ou pour la réception. Les 1 000 premières connexions réparties entre tous les espaces de noms standard d’un abonnement Azure sont incluses sans frais supplémentaires (hors frais de base). Étant donné que ces volumes couvrent de nombreux scénarios de messagerie de service à service, des frais de connexion répartie sont habituellement pertinents si vous prévoyez d'utiliser l'interrogation longue AMQP ou HTTP avec un grand nombre de clients. Par exemple, pour obtenir un flux d'événements plus efficace ou permettre une communication bidirectionnelle avec de nombreux appareils ou instances d'application.

## <a name="next-steps"></a>étapes suivantes

* Pour plus d’informations sur la tarification Service Bus, consultez la [page Tarification de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Consultez le [FAQ sur Service Bus](service-bus-faq.md#pricing) pour quelques questions courantes sur la tarification et facturation de Service Bus.

[Azure portal]: https://portal.azure.com
