---
title: Prise en main des connexions hybrides Azure Relay dans Node | Microsoft Docs
description: "Écrivez une application console Node.js pour les connexions hybrides Azure Relay."
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: d79c05253da568d3a9b2f2912d22eab2df586821
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>Prise en main des connexions hybrides Relay

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Ce tutoriel présente les [connexions hybrides Azure Relay](relay-what-is-it.md#hybrid-connections) et explique comment utiliser Node.js pour créer une application cliente qui envoie des messages à une application d’écouteur correspondante. 

## <a name="what-will-be-accomplished"></a>Les opérations que nous allons effectuer

Étant donné que les Connexions hybrides nécessitent un composant client et un composant serveur, vous allez créer deux applications console dans ce tutoriel. Voici la procédure à suivre :

1. Créer un espace de noms Relay à l’aide du portail Azure.
2. Créer une connexion hybride, à l’aide du portail Azure.
3. Écrire une application de console de serveur pour recevoir des messages.
4. Écrire une application de console de client pour envoyer des messages.

## <a name="prerequisites"></a>Prérequis

1. [Node.js](https://nodejs.org/en/).
2. Un abonnement Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Créer un espace de noms à l’aide du Portail Azure

Si vous disposez déjà créé un espace de noms Relay, passez à la section [Créer une connexion hybride à l’aide du portail Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Créer une connexion hybride à l’aide du portail Azure

Si vous disposez déjà d’une connexion hybride, passez à la section [Créer une application de serveur](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Créer une application de serveur (récepteur)

Pour écouter et recevoir des messages à partir de Relay, écrivez une application console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Créer une application cliente (expéditeur)

Pour envoyer des messages à Relay, écrivez une application console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Exécution des applications

1. Exécutez l’application serveur : à partir d’un type d’invite de commandes Node.js `node listener.js`.
2. Exécutez l’application cliente : à partir d’un type d’invite de commandes Node.js `node sender.js`, puis entrez du texte.
3. Vérifiez que la console d’application de serveur renvoie le texte entré dans l’application cliente.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Vous avez créé une application de connexions hybrides complète avec Node.js : félicitations !

## <a name="next-steps"></a>Étapes suivantes

* [FAQ sur Azure Relay](relay-faq.md)
* [Créer un espace de noms](relay-create-namespace-portal.md)
* [Prise en main de .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Prise en main de Node](relay-hybrid-connections-node-get-started.md)

