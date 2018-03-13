---
title: Filtres de rubrique Azure Service Bus | Microsoft Docs
description: Filtrer les rubriques Azure Service Bus
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 305c017bd49f233c10479e2c33ec8db72cae3aa7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="topic-filters-and-actions"></a>Actions et filtres de rubrique

Les abonnés peuvent définir les messages qu’ils veulent recevoir d’une rubrique. Ces messages sont spécifiés sous la forme d’une ou de plusieurs règles d’abonnement nommées. Chaque règle se compose d’une condition qui sélectionne des messages spécifiques et d’une action qui annote le message sélectionné. Pour chaque condition de règle de correspondance, l’abonnement crée une copie du message, qui peut être annotée différemment selon la règle de correspondance utilisée.

Chaque nouvel abonnement à une rubrique est associé à une règle d’abonnement par défaut initiale. Si vous ne spécifiez pas explicitement une condition de filtre pour la règle, le filtre **true** est appliqué et sélectionne tous les messages dans l’abonnement. La règle par défaut n’est associée à aucune action d’annotation.

Service Bus prend en charge trois conditions de filtre :

-   *Filtres booléens* : **TrueFilter** et **FalseFilter** sélectionnent tous les messages entrants (**true**) ou aucun des messages entrants (**false**) de l’abonnement.

-   *Filtres SQL* : **SqlFilter** contient une expression conditionnelle de type SQL qui est évaluée dans le répartiteur par rapport aux propriétés utilisateur et système des messages entrants. Toutes les propriétés système doivent avoir le préfixe `sys.` dans l’expression conditionnelle. Le [sous-ensemble du langage SQL pour les conditions de filtre](service-bus-messaging-sql-filter.md) recherche la présence de propriétés (EXISTS), de valeurs Null (IS NULL), d’opérateurs de relation logiques NOT/AND/OR, d’une arithmétique numérique simple et de critères spéciaux de texte simples avec LIKE.

-   *Filtres de corrélation* : **CorrelationFilter** contient un ensemble de conditions qui sont mises en correspondance par rapport à une ou plusieurs propriétés utilisateur et système d’un message entrant. En général, la mise en correspondance s’effectue par rapport à la propriété **CorrelationId**, mais l’application peut également choisir de le faire par rapport aux propriétés **ContentType**, **Label**, **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId** ou **To** et à des propriétés définies par l’utilisateur. Il y a correspondance quand la valeur d’une propriété d’un message entrant est identique à la valeur spécifiée dans le filtre de corrélation. Pour les expressions de chaîne, la comparaison respecte la casse. Quand vous spécifiez plusieurs propriétés de correspondance, le filtre les combine en une condition AND logique, ce qui implique que toutes les conditions du filtre doivent être remplies pour qu’il y ait correspondance.

Tous les filtres évaluent les propriétés des messages. Ils ne peuvent pas évaluer le corps des messages.

Les règles de filtre complexes nécessitent une plus grande capacité de traitement. En particulier, l’utilisation de règles de filtre SQL diminue le débit global des messages dans les abonnements, rubriques et espaces de noms. Dans la mesure du possible, les applications doivent utiliser des filtres de corrélation plutôt que des filtres de type SQL, car les filtres de corrélation étant beaucoup plus performants, ils ont moins d’impact sur le débit.

## <a name="actions"></a>Actions

Avec les conditions de filtre SQL, et uniquement avec ce type de conditions, vous pouvez définir une action qui annote le message en ajoutant, en supprimant ou en remplaçant des propriétés et leurs valeurs. L’action [utilise une expression de type SQL](service-bus-messaging-sql-filter.md) dont la syntaxe se rapproche de celle de l’instruction SQL UPDATE. L’action est effectuée sur le message après la mise en correspondance du message et avant la sélection du message dans la rubrique. Les modifications apportées aux propriétés du message s’appliquent uniquement au message copié dans l’abonnement.

## <a name="usage-patterns"></a>Modèles d’usage

Le scénario d’usage le plus simple pour une rubrique est que chaque abonnement récupère une copie de chaque message envoyé dans une rubrique, pour obtenir un modèle de diffusion.

Les filtres et les actions implémentent deux autres groupes de modèles : le partitionnement et le routage.

Le partitionnement utilise des filtres pour distribuer les messages aux différents abonnements à une rubrique existants de manière prévisible et en mode mutuellement exclusif. Le modèle de partitionnement est utilisé quand la taille des instances d’un système est augmentée pour traiter de nombreux contextes différents dans des compartiments fonctionnellement identiques qui contiennent chacun une partie de l’ensemble des données, par exemple, les informations de profil d’un client. Avec le partitionnement, un éditeur envoie le message dans une rubrique sans avoir besoin de connaître le modèle de partitionnement. Le message est ensuite déplacé vers l’abonnement approprié, à partir duquel il peut ensuite être récupéré par le gestionnaire de messages de la partition.

Le routage utilise des filtres pour distribuer les messages aux différents abonnements à la rubrique de manière prévisible, mais pas nécessairement en mode mutuellement exclusif. Utilisés conjointement avec la fonctionnalité de [transfert automatique](service-bus-auto-forwarding.md), les filtres de rubrique permettent de créer des graphiques de routage complexes dans un espace de noms Service Bus pour la distribution de messages dans une région Azure. Avec Azure Functions ou Azure Logic Apps servant de pont entre les espaces de noms Azure Service Bus, vous pouvez créer des topologies globales complexes avec une intégration directe dans les applications métier.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Syntaxe SQLFilter](service-bus-messaging-sql-filter.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)