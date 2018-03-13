---
title: "Créer un espace de noms Service Bus à l’aide du portail Azure | Microsoft Docs"
description: "Créez un espace de noms Service Bus à l’aide du portail Azure."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/22/2018
ms.author: sethm
ms.openlocfilehash: a24fa21848005d9768d26ae865680a4851e1dd81
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Créer un espace de noms Service Bus à l’aide du Portail Azure

Un espace de noms est un conteneur d’étendue pour tous les composants de messagerie. Plusieurs files d’attente et rubriques peuvent résider dans un seul espace de noms, et les espaces de noms servent souvent de conteneurs d’applications. Il existe deux façons de créer un espace de noms Service Bus :

1. Portail Azure (cet article)
2. [Modèles Microsoft Azure Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Créer un espace de noms dans le Portail Azure

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Félicitations ! Vous venez de créer un espace de noms de messagerie Service Bus.

## <a name="next-steps"></a>Étapes suivantes

Consultez les [exemples GitHub][github-samples], qui illustrent certaines des fonctionnalités les plus avancées de la messagerie Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure/azure-service-bus/tree/master/samples
