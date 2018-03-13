---
title: "Paramètres de port d’Azure Relay | Microsoft Docs"
description: "Fournit des informations sur les valeurs de port d’Azure Relay."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 055f04d496b56a5e8542911aa78292d7746ae80b
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-port-settings"></a>Paramètres de port d’Azure Relay

Le tableau suivant décrit la configuration requise pour les valeurs de port d’Azure Relay.

## <a name="hybrid-connections"></a>Connexions hybrides
Les connexions hybrides font appel à WebSockets comme mécanisme de transport sous-jacent, qui utilise **HTTPS** uniquement. 

## <a name="wcf-relays"></a>Relais WCF
  
|Liaison|Sécurité de transport|Port|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding Class](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|OUI|HTTPS| 
| |" |Non |HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|OUI|9351/HTTPS|  
||" |Non |9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/service)|Vous pouvez soit utiliser|5671/9352/HTTP (9352/9353 en cas d’utilisation hybride)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|OUI|9351/HTTPS|  
||" |Non |9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|OUI|HTTPS|  
||" |Non |HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|OUI|HTTPS|  
||" |Non |HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure Relay, consultez les liens suivants :
* [Qu’est-ce qu’Azure Relay ?](relay-what-is-it.md)
* [FAQ Relay](relay-faq.md)