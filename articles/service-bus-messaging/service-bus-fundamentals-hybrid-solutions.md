---
title: "Vue d’ensemble des notions de base d’Azure Service Bus | Microsoft Docs"
description: "Présentation de l’utilisation de Service Bus pour connecter les applications Azure à d’autres logiciels."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 12654cdd-82ab-4b95-b56f-08a5a8bbc6f9
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: fab765480a2f480e8c54035d903d24843490ee38
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-service-bus"></a>Azure Service Bus

Que l’application ou le service s’exécute dans le cloud ou localement, il est souvent en interaction avec d’autres applications ou services. Afin de répondre à ce besoin de façon globale, Microsoft Azure contient Service Bus. Cet article aborde la technologie Service Bus, décrit ce qu’elle fait et présente des exemples d’utilisation.

## <a name="service-bus-fundamentals"></a>Concepts de base de Service Bus

À chaque situation correspond un style de communication. Parfois, laisser les applications envoyer et recevoir des messages via une simple file d’attente suffit. Dans d’autres situations, une file d’attente ordinaire n’est pas suffisante et une file avec mécanisme de publication et d’abonnement est une meilleure solution. Dans certains cas, vous avez simplement besoin d’une connexion entre les applications, sans file d’attente. Azure Service Bus offre ces trois options, ce qui permet à vos applications d’interagir de différentes manières.

Service Bus est un service cloud mutualisé, ce qui signifie que le service est partagé par plusieurs utilisateurs. Chaque utilisateur, par exemple un développeur d’applications, crée un *espace de noms*, puis définit les mécanismes de communication nécessaires au sein de ce dernier. La Figure 1 montre à quoi ressemble cette architecture :

![][1]

**Figure 1 : Service Bus est un service mutualisé permettant la connexion d’applications via le cloud.**

Dans un espace de noms, vous pouvez utiliser une ou plusieurs instances de trois mécanismes de communication distincts, chacun se connectant de manière différente à l’application. Les choix sont les suivants :

* Les *files d’attente* permettent la communication unidirectionnelle. Chacune agit comme un intermédiaire (ou *broker*) qui stocke les messages envoyés jusqu’à leur réception. Chaque message est reçu par un destinataire unique.
* Les *rubriques* fournissent une communication unidirectionnelle à l’aide *d’abonnements* : une seule rubrique peut avoir plusieurs abonnements. À l’instar d’une file d’attente, une rubrique agit comme un intermédiaire, mais chaque abonnement peut utiliser un filtre pour recevoir uniquement les messages correspondant à un critère spécifique.
* Les *relais* permettent la communication bidirectionnelle. À l’inverse des files d’attente et des rubriques, le relais ne stocke pas les messages en transit ; il ne s’agit pas d’un intermédiaire. Il ne fait que les transférer vers l’application de destination.

Lorsque vous créez une file d'attente, une rubrique ou un relais, vous lui donnez un nom. Ce nom, combiné à celui de votre espace de noms, donne un identificateur unique à l’objet. Les applications peuvent fournir ce nom à Service Bus, puis utiliser cette file d’attente, cette rubrique ou ce relais pour communiquer entre elles. 

Pour utiliser ces objets dans le scénario de relais, les applications Windows peuvent utiliser Windows Communication Foundation (WCF). Ce service est appelé [Relais WCF](../service-bus-relay/relay-what-is-it.md). Pour les files d’attente et les rubriques, les applications Windows peuvent utiliser des API de messagerie définie par Service Bus. Pour faciliter l’utilisation de ces objets à partir d’applications non-Windows, Microsoft fournit des Kits de développement logiciel (SDK) pour Java, Node.js et d’autres langages. Vous pouvez également accéder aux files d’attente et aux rubriques à l’aide des [API REST](/rest/api/servicebus/) sur HTTP(s). 

Il est important de comprendre que même si Service Bus fonctionne dans le cloud (c'est-à-dire dans les centres de données Microsoft Azure), les applications qui y ont recours peuvent s'exécuter n'importe où. Vous pouvez utiliser Service Bus pour connecter des applications qui s’exécutent sous Azure, par exemple, ou des applications qui s’exécutent dans votre centre de données. Vous pouvez également l’utiliser pour connecter une application qui s’exécute sous Azure ou une autre plateforme cloud avec une application locale ou avec des tablettes et des téléphones. Il est également possible de connecter des appareils électroménagers, des capteurs ou d’autres appareils à une application centrale ou de les connecter entre eux. Service Bus est un mécanisme de communication générique dans le cloud, accessible quasiment partout. La façon dont vous l’utilisez dépend des besoins de vos applications.

## <a name="queues"></a>Files d’attente

Supposons que vous décidiez de connecter deux applications à l'aide d'une file d'attente Service Bus. La Figure 2 illustre cette situation :

![][2]

**Figure 2 : les files d’attente Service Bus offrent un système de files d’attente unidirectionnelles asynchrones.**

L’expéditeur envoie un message à une file d’attente Service Bus et le destinataire le récupère plus tard. Une file d’attente peut avoir un seul destinataire, comme l’indique la Figure 2, ou plusieurs applications peuvent lire à partir de la même file d’attente. Dans ce dernier cas, chaque message est lu par un seul destinataire. Dans le cas d’un service de multidiffusion, utilisez plutôt une rubrique.

Chaque message se compose de deux parties : un jeu de propriétés (chacun de type paire clé/valeur) et une charge utile de message. La charge utile peut être de type binaire, texte, voire XML. La façon dont les messages sont utilisés dépend de ce que tente de faire l’application. Par exemple, une application qui envoie un message à propos d’une vente récente peut mentionner les propriétés **Seller="Ava"** et **Amount=10000**. Le corps du message peut contenir une image numérisée du contrat signé ou rester vide s’il n’y en a pas.

Le destinataire peut lire le message de file d’attente Service Bus de deux façons. La première option, appelée *[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)*, reçoit un message de la file d’attente et le supprime immédiatement. C’est simple, mais si le destinataire rencontre un problème avant d’avoir fini de traiter le message, ce dernier est perdu. Comme il est retiré de la file d’attente, aucun autre destinataire ne peut y accéder. 

La deuxième option, *[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)*, a pour but de résoudre ce problème. Comme dans le cas de **ReceiveAndDelete**, la lecture **PeekLock** retire le message de la file d’attente. Par contre, le message n’est pas supprimé. Il est verrouillé, et donc désormais invisible par les autres destinataires. Il attend ensuite un des trois événements suivants :

* Si le destinataire traite correctement le message, il appelle la méthode [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync), et la file d’attente supprime le message. 
* Si le destinataire décide qu’il ne peut pas traiter correctement le message, il appelle la méthode [Abandon()](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync). La file d’attente déverrouille le message et le remet à disposition des autres destinataires.
* Si le destinataire n’appelle aucune de ces méthodes pendant une période réglable (60 secondes par défaut), la file d’attente part du principe que le destinataire a échoué. Dans ce cas, elle se comporte comme si le destinataire avait passé l’appel **Abandon**, rendant le message accessible aux autres destinataires.

Notez ce qui peut se produire ici : le même message risque d’être remis deux fois, peut-être à deux destinataires différents. Les applications qui utilisent des files d’attente Service Bus doivent pouvoir faire face à cet événement. Afin de faciliter la détection des doublons, chaque message comporte une propriété [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid#Microsoft_Azure_ServiceBus_Message_MessageId) unique, qui reste la même par défaut quel que soit le nombre de lectures du message dans la file d’attente. 

Les files d’attente sont utiles dans de nombreuses situations. Elles permettent aux applications de communiquer, même si elles ne s’exécutent pas toutes les deux en même temps, ce qui peut s’avérer utile avec les applications mobiles et les applications de traitement par lots. Une file d'attente avec plusieurs destinataires assure aussi un équilibrage automatique de la charge, car les messages sont répartis vers ces différents destinataires.

## <a name="topics"></a>Rubriques

Même si elles sont utiles, les files d'attente ne sont pas toujours la bonne solution. Parfois, les rubriques sont préférables. La Figure 3 illustre cette idée :

![][3]

**Figure 3 : en fonction du filtre spécifié par l’application, celle-ci peut recevoir certains messages ou tous les messages envoyés à une rubrique Service Bus.**

Les *rubriques* sont assez similaires aux files d’attente. Les expéditeurs envoient les messages à la rubrique de la même façon qu’ils envoient des messages dans la file d’attente. Ces messages ont le même aspect que dans la file d’attente. La différence est que les rubriques permettent à chaque application réceptrice de créer son propre *abonnement* et éventuellement de définir un *filtre*. L’abonné ne voit alors que les messages correspondant à ce filtre. Par exemple, la figure 3 présente un expéditeur et une rubrique avec trois abonnés, chacun disposant de son propre filtre :

* L’abonné 1 ne reçoit que les messages contenant la propriété *Seller="Ava"*.
* L’abonné 2 reçoit les messages qui contiennent la propriété *Seller="Ruby"* et/ou la propriété *Amount* avec une valeur de 100 000 ou plus. Si Ruby est la directrice des ventes, elle souhaite peut-être pouvoir afficher ses propres ventes, ainsi que toutes les ventes importantes, quel que soit le vendeur.
* L’abonné 3 a défini son filtre sur *True*, ce qui veut dire qu’il reçoit tous les messages. Par exemple, cette application peut être chargée de maintenir une piste d’audit et doit donc voir tous les messages.

Comme pour les files d’attente, les abonnés d’une rubrique peuvent lire les messages à l’aide de l’option [ReceiveAndDelete ou PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode). À l’inverse des files d’attente cependant, un message unique envoyé à une rubrique peut être reçu par plusieurs abonnements. Cette approche, communément appelée *publication et abonnement* (ou *pub/sub*), est utile quand plusieurs applications sont intéressées par les mêmes messages. En définissant le filtre approprié, chaque abonné peut récupérer la partie du flux de messages qu’il souhaite voir.

## <a name="relays"></a>relais

Les files d’attente et les rubriques permettent la communication asynchrone unidirectionnelle via un intermédiaire. Le trafic circule dans une seule direction, et il n’y a pas de connexion directe entre expéditeur et destinataire. Mais que faire si vous ne voulez pas de cette connexion ? Supposons que vos applications doivent aussi bien envoyer que recevoir des messages, ou bien que vous souhaitiez disposer d’une liaison directe entre elles et que vous n’avez pas besoin d’un intermédiaire pour stocker les messages. Pour ce genre de scénarios, Service Bus fournit des *relais*, comme illustré dans la Figure 4 :

![][4]

**Figure 4 : le relais Service Bus permet la communication bidirectionnelle synchrone entre applications.**

La question évidente à poser à propos des relais est la suivante : pourquoi y recourir ? Même si je n’ai pas besoin de files d’attente, pourquoi faire communiquer les applications via un service cloud au lieu de les faire interagir directement ? La réponse est que de les faire communiquer directement peut s’avérer plus compliqué qu’il n’y paraît.

Supposons que vous souhaitiez connecter deux applications locales s’exécutant dans les centres de données de l’entreprise. Chaque application se trouve derrière un pare-feu et chaque centre de données utilise probablement la traduction d’adresses réseau. Le pare-feu bloque les données entrantes sur presque tous les ports, et la traduction d’adresses réseau indique que l’ordinateur sur lequel s’exécutent les applications ne dispose pas d’une adresse IP fixe que vous pouvez joindre directement depuis l’extérieur du centre de données. Sans aide supplémentaire, la connexion de ces applications via Internet public pose problème.

Un Service Bus Relay peut être utile. Afin d’établir une communication bidirectionnelle via un relais, chaque application établit une connexion TCP sortante avec Service Bus, et la maintient ouverte. Toutes les communications entre les deux applications transitent par ces connexions. Comme chaque connexion a été établie depuis le centre de données, le pare-feu autorise le trafic entrant vers chaque application sans ouvrir de nouveaux ports. Cette approche contourne également le problème de la traduction d’adresses réseau, car chaque application dispose d’un point de terminaison constant dans le cloud pendant toute la durée de la communication. En échangeant des données via le relais, les applications peuvent éviter les problèmes qui pourraient rendre la communication difficile. 

Pour utiliser les relais Service Bus, les applications s’appuient sur Windows Communication Foundation (WCF) Service Bus comporte des liaisons WCF qui simplifient l’interaction des applications Windows via les relais. Les applications qui utilisent déjà WCF peuvent normalement ne spécifier qu’une seule de ces liaisons, puis ensuite communiquer via un relais. Cependant, à l’inverse des files d’attente et des rubriques, et même si elle reste possible, l’utilisation de relais à partir d’applications non-Windows demande un effort de programmation certain. En effet, aucune bibliothèque standard n’est fournie.

Contrairement aux files d’attente et aux rubriques, les applications ne créent pas de relais de façon explicite. Lorsqu’une application qui souhaite recevoir des messages établit une connexion TCP avec Service Bus, un relais est automatiquement créé. Ce dernier est supprimé une fois la connexion abandonnée. Pour qu’une application trouve le relais créé par un écouteur spécifique, Service Bus fournit un registre qui permet aux applications de retrouver un relais grâce à son nom.

Lorsque vous avez besoin d’une communication directe entre les applications, les relais constituent la meilleure solution. Prenons par exemple le système de réservation d’une compagnie aérienne qui s’exécute sur un centre de données local qui peut être accessible à partir de bornes d’enregistrement, d’appareils mobiles et d’ordinateurs. Les applications qui s’exécutent sur tous ces systèmes peuvent s’appuyer sur les relais Service Bus dans le cloud pour communiquer, quel que soit l’endroit où elles s’exécutent.

## <a name="summary"></a>Résumé

La connexion d’applications a toujours fait partie de l’assemblage de solutions complètes, et l’éventail de scénarios qui requièrent des services et des applications pour communiquer entre eux augmentera, étant donné que de plus en plus d’applications et de périphériques sont connectés à Internet. En fournissant pour cela des technologies cloud comme les files d’attente, les rubriques et les relais, Service Bus vise à rendre cette fonction essentielle encore plus simple à implémenter et plus largement accessible.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les principes de base d’Azure Service Bus, consultez ces liens pour en savoir plus :

* Utilisation des [files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* Utilisation des [rubriques Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* Utilisation des [relais Service Bus](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
* [Exemples Service Bus](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png
