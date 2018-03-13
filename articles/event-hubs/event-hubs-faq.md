---
title: Forum Aux Questions (FAQ) sur Azure Event Hubs | Microsoft Docs
description: Forum Aux Questions (FAQ) sur Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 6bdcbbe37613d5384017409f3be2772085e276ae
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="event-hubs-frequently-asked-questions"></a>Forum Aux Questions (FAQ) sur Event Hubs

## <a name="general"></a>Généralités

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Quelle est la différence entre les niveaux De Base et Standard pour les hubs d’événements ?

Le niveau Standard d’Azure Event Hubs fournit des fonctionnalités supérieures à celles disponibles dans le niveau de base. Les fonctionnalités suivantes sont incluses dans le niveau Standard :
* Rétention plus longue des événements
* Connexions réparties supplémentaires, avec frais supplémentaires pour le dépassement du nombre inclus
* Plus d’un seul groupe de consommateurs
* [Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

Pour plus d’informations sur les niveaux tarifaires, notamment pour Event Hubs Dedicated, consultez la rubrique [Informations tarifaires pour Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Que sont les unités de débit des hubs d'événements ?

Vous devez sélectionner explicitement les unités de débit Event Hubs via le portail Azure ou les modèles Resource Manager Event Hubs. Les unités de débit s’appliquent à tous les concentrateurs d’événements dans un espace de noms Event Hubs et chaque unité de débit fournit à l’espace de noms les capacités suivantes :

* Jusqu’à 1 Mo par seconde d’événements d’entrée (événements envoyés à un concentrateur d’événements), mais pas plus de 1 000 événements d’entrée, opérations de gestion ou appels d’API de contrôle par seconde.
* Jusqu’à 2 Mo par seconde d’événements de sortie (événements consommés à partir d’un concentrateur d’événements).
* Jusqu'à 84 Go de stockage d'événements (suffisant pour la période de rétention de 24 heures par défaut).

Les unités de débit des hubs d'événements sont facturées par heure, en fonction du nombre maximal d'unités sélectionnées pendant l'heure donnée. Vous pouvez [augmenter automatiquement le nombre d’unités de débit](event-hubs-auto-inflate.md) à mesure que votre utilisation augmente.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Comment les limites des unités de débit des hubs d'événements sont-elles appliquées ?
Si le débit d’entrée total ou le taux d’événements d’entrée total de tous les concentrateurs d’événements d’un espace de noms dépassent les allocations d’unité de débit agrégées, les expéditeurs sont limités et reçoivent des erreurs indiquant que le quota d’entrée a été dépassé.

Si le débit de sortie total ou le taux d’événements de sortie total de tous les concentrateurs d’événements d’un espace de noms dépassent les allocations d’unité de débit agrégées, les récepteurs sont limités et reçoivent des erreurs indiquant que le quota de sortie a été dépassé. Les quotas d’entrée et de sortie sont appliqués séparément afin qu’aucun expéditeur n’entraîne un ralentissement de la consommation d’événements ni qu’aucun récepteur n’empêche l’envoi d’événements à un concentrateur d’événements.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Y a-t-il une limite au nombre d'unités de débit qui peuvent être sélectionnées ?
Par défaut, il existe un quota de 20 unités de débit par espace de noms. Vous pouvez demander un quota d'unités de débit supérieur en soumettant un ticket de support. Au-delà de la limite de 20 unités de débit, des regroupements 20 et 100 unités de débit sont disponibles. Notez que l'utilisation de plus de 20 unités de débit empêche toute modification du nombre d'unités de débit sans soumission d’un ticket de support.

La fonctionnalité de [majoration automatique](event-hubs-auto-inflate.md) vous permet d’augmenter automatiquement le nombre d’unités de débit à mesure que votre utilisation augmente.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Puis-je utiliser une connexion AMQP unique pour l’envoi et la réception à partir de plusieurs concentrateurs d’événements ?
Oui, à condition que la totalité des concentrateurs d’événements se trouve dans le même espace de noms.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Quelle est la période de rétention maximale pour les événements ?
Le niveau Standard des hubs d'événements prend actuellement en charge une période de rétention maximale de 7 jours. Notez que les concentrateurs d’événements ne sont pas destinés à servir de magasin de données permanent. Les périodes de rétention supérieures à 24 heures sont destinées aux scénarios dans lesquels il est utile de pouvoir reproduire un flux d'événements sur les mêmes systèmes ; par exemple, pour tester ou vérifier un nouveau modèle d’apprentissage de machine sur des données existantes. Si vous avez besoin que la rétention des messages dure plus de 7 jours, activez [Event Hubs Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) dans votre hub d’événements pour en extraire les données et les transférer vers le compte de stockage ou le compte Azure Data Lake Service de votre choix. L’activation de Capture est facturée en fonction de votre unité de débit achetée.

### <a name="where-is-azure-event-hubs-available"></a>Où Azure Event Hubs est-il disponible ?
Azure Event Hubs est disponible dans toutes les régions Azure prises en charge. Pour obtenir une liste, visitez la page [Régions Azure](https://azure.microsoft.com/regions/).  

## <a name="best-practices"></a>Meilleures pratiques

### <a name="how-many-partitions-do-i-need"></a>De combien de partitions ai-je besoin ?

Notez que le nombre de partitions sur un hub d’événements ne peut pas être modifié après configuration. Avec cela à l’esprit, il est important de réfléchir au nombre de partitions dont vous avez besoin avant de commencer. 

Event Hubs est conçu pour autoriser un lecteur de partition unique par groupe de consommateurs. Dans la plupart des cas, le paramètre par défaut de quatre partitions est suffisant. Si vous cherchez mettre à l’échelle le traitement des événements, vous souhaiterez peut-être ajouter des partitions supplémentaires. Il n’y a aucune limite de débit spécifique sur une partition, mais le débit cumulé dans votre espace de noms est limité par le nombre d’unités de débit. Lorsque vous augmentez le nombre d’unités de débit de votre espace de noms, vous souhaiterez peut-être avoir des partitions supplémentaires pour permettre aux lecteurs fonctionnant simultanément d’atteindre leur débit maximal.

Toutefois, si vous disposez d’un modèle dans lequel votre application a une affinité avec une partition particulière, l’augmentation du nombre de partitions pourrait n’offrir aucun avantage. Pour plus d’informations à ce sujet, consultez [Disponibilité et cohérence](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Tarifs

### <a name="where-can-i-find-more-pricing-information"></a>Où puis-je obtenir des informations complémentaires sur la tarification ?
Pour des informations complètes sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Existe-t-il des frais pour la rétention de plus de 24 heures des événements de hubs d'événements ?
Le niveau Standard Event Hubs permet de conserver les messages au-delà de 24 heures, avec un maximum de 7 jours. Si la taille du nombre total d’événements stockés dépasse l’allocation de stockage pour le nombre d’unités de débit sélectionnées (84 Go par unité de débit), la taille excédentaire est facturée au tarif de stockage Blob Azure publié. L'allocation de stockage dans chaque unité de débit couvre tous les frais de stockage pour les périodes de rétention de 24 heures (par défaut), même si l'unité de débit est utilisée pour l'allocation d'entrée maximale.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Comment la taille de stockage des hubs d'événements est-elle calculée et facturée ?
La taille totale de tous les événements stockés, notamment de toute surcharge interne pour les en-têtes d’événements ou les structures de stockage sur disque dans tous les concentrateurs d’événements, est mesurée tout au long de la journée. À la fin de la journée, la taille maximale de stockage est calculée. L'allocation de stockage quotidienne est calculée en fonction du nombre minimal d'unités de débit qui ont été sélectionnées pendant la journée (chaque unité de débit fournit une allocation de 84 Go). Si la taille totale dépasse l'allocation de stockage quotidienne calculée, l'excédent de stockage est facturé selon les taux de stockage Azure Blob (au taux **Stockage localement redondant** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Comment les événements d'entrée des hubs d'événements sont-ils calculés ?
Chaque événement envoyé à un concentrateur d’événements est considéré comme un message facturable. Un *événement d’entrée* est défini comme une unité de données inférieure ou égale à 64 Ko. Tout événement dont la taille est inférieure ou égale à 64 Ko est considéré comme un événement facturable. Si l’événement est supérieur à 64 Ko, le nombre d’événements facturables est calculé en fonction de la taille de l’événement, par multiples de 64 Ko. Par exemple, un événement de 8 Ko envoyé au concentrateur d’événements est facturé comme un événement, mais un message de 96 Ko envoyé au concentrateur d’événements est facturé comme deux événements.

Les événements utilisés à partir d’un concentrateur d’événements, ainsi que les opérations de gestion et les appels de contrôle comme les points de contrôle, ne sont pas comptabilisés comme des événements d’entrée facturables, mais s’ajoutent à l’allocation d’unité de débit.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Des frais de connexion répartie s'appliquent-ils aux hubs d'événements ?
Des frais de connexion s'appliquent uniquement lorsque le protocole AMQP est utilisé. Il n'y a aucun frais de connexion pour l'envoi d'événements à l'aide de HTTP, quel que soit le nombre de systèmes ou de périphériques d’envoi. Si vous prévoyez d’utiliser AMQP (par exemple, pour améliorer le flux d’événements ou activer la communication bidirectionnelle sur des scénarios de commande et de contrôle IoT), reportez-vous à la page [Informations tarifaires pour Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) pour plus de détails sur le nombre de connexions incluses dans chaque niveau de service.

### <a name="how-is-event-hubs-capture-billed"></a>Comment est facturé Event Hubs Capture ?
Capture est activé lorsque la fonctionnalité de capture est activée pour chaque concentrateur d’événements de l’espace de noms. Event Hubs Capture est facturé à l’heure, par unité de débit achetée. La facturation d’Event Hubs Capture reflète les variations du nombre d’unités de débit sous forme d’incréments d’heure pleine. Pour plus d’informations sur la facturation d’Event Hubs Capture, consultez [Informations tarifaires pour Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Serai-je facturé pour le compte de stockage que je sélectionne pour Event Hubs Capture ?
Capture utilise un compte de stockage que vous fournissez lorsqu’il est activé sur un concentrateur d’événements. Ce compte de stockage étant le vôtre, les éventuels changements de configurations seront facturés sur votre abonnement Azure.

## <a name="quotas"></a>Quotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Y a-t-il des quotas associés aux Event Hubs ?
Pour obtenir la liste de tous les quotas d’Event Hubs, consultez la page [quotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quelles sont les exceptions générées par Event Hubs et les actions recommandées ?
Pour obtenir la liste des exceptions Event Hubs potentielles, consultez la page [Vue d’ensemble des exceptions](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Journaux de diagnostic
Event Hubs prend en charge deux types de [journaux de diagnostics](event-hubs-diagnostic-logs.md) (journaux d’erreurs Capture et journaux des opérations), qui sont tous deux représentés en json et peuvent être activés via le portail Azure.

### <a name="support-and-sla"></a>Prise en charge et contrats SLA
Un support technique pour les hubs d'événements est disponible via les [forums de la communauté](https://social.msdn.microsoft.com/forums/azure/home). La gestion de la facturation et des abonnements est fournie gratuitement.

Pour en savoir plus sur notre contrat SLA, consultez la section [Contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d'ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Créer un hub d’événements](event-hubs-create.md)
* [Majoration automatique Event Hubs](event-hubs-auto-inflate.md)
