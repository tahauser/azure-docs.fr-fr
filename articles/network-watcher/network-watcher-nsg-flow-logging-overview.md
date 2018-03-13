---
title: "Présentation de la journalisation des flux pour les groupes de sécurité réseau avec Azure Network Watcher | Microsoft Docs"
description: "Cette page explique comment utiliser les journaux de flux de groupe de sécurité réseau, une fonctionnalité d’Azure Network Watcher"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4eaffba08ccf601e440709d804891668340a376d
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Présentation de la journalisation des flux pour les groupes de sécurité réseau

Les journaux de flux de groupe de sécurité réseau désignent une fonctionnalité de Network Watcher qui vous permet d’afficher des informations sur le trafic IP entrant et sortant d’un groupe de sécurité réseau. Ces journaux de flux sont écrits au format json et affichent les flux entrants et sortants en fonction de règles, de la carte réseau à laquelle le flux s’applique, des informations à 5 tuples sur le flux (adresse IP source/de destination, port source/de destination, protocole), et de l’autorisation ou du refus du trafic.

![vue d’ensemble des journaux de flux][1]

Même si les journaux de flux ciblent les groupes de sécurité réseau, ils ne sont pas affichés de la même façon que les autres journaux. Les journaux de flux sont uniquement stockés dans un compte de stockage et suivent le chemin de journalisation comme indiqué dans l’exemple ci-après :

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Les mêmes stratégies de rétention que celles des autres journaux s’appliquent aux journaux de flux. Les journaux ont une stratégie de rétention qui peut être définie dans une plage comprise entre 1 et 365 jours. Si aucune stratégie de rétention n’est définie, les journaux sont conservés indéfiniment.

## <a name="log-file"></a>Fichier journal

Les journaux de flux ont plusieurs propriétés. La liste suivante répertorie les propriétés qui sont renvoyées dans le journal de flux de groupe de sécurité réseau :

* **time** - L’heure à laquelle l’événement a été journalisé.
* **systemId** - L’ID de la ressource du groupe de sécurité réseau.
* **category** - La catégorie de l’événement ; il s’agit toujours de NetworkSecurityGroupFlowEvent.
* **resourceid** - L’ID de la ressource du groupe de sécurité réseau.
* **operationName** - Toujours NetworkSecurityGroupFlowEvents.
* **properties** - Une collection des propriétés du flux.
    * **Version** - Le numéro de version du schéma d’événement du journal de flux.
    * **flow** - Une collection de flux. Cette propriété a plusieurs entrées pour différentes règles.
        * **rule** - La règle pour laquelle les flux sont répertoriés.
            * **flows** - Une collection de flux.
                * **mac** - L’adresse MAC de la carte réseau de la machine virtuelle où le flux a été collecté.
                * **flowTuples** - Une chaîne qui contient plusieurs propriétés séparées par des virgules pour le tuple de flux.
                    * **Time Stamp** - Cette valeur indique la date et l’heure du flux au format UNIX EPOCH.
                    * **Source IP** - L’adresse IP source.
                    * **Destination IP** - L’adresse IP de destination.
                    * **Source Port** - Le port source.
                    * **Destination Port** - Le port de destination.
                    * **Protocol** - Le protocole du flux. Les valeurs valides sont **T** pour TCP et **U** pour UDP.
                    * **Traffic Flow** - La direction du flux de trafic. Les valeurs valides sont **I** pour le trafic entrant et **O** pour le trafic sortant.
                    * **Traffic** - Indique si le trafic a été autorisé ou refusé. Les valeurs valides sont **A** pour autorisé et **D** pour refusé.


Vous trouverez ci-dessous un exemple de journal de flux. Comme vous pouvez le voir, il existe plusieurs enregistrements qui suivent la liste des propriétés décrite dans la section précédente. 

> [!NOTE]
> Les valeurs de la propriété flowTuples sont une liste séparée par des virgules.
 
```json
{
    "records": 
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment activer les journaux de flux en consultant [Enable flow logs](network-watcher-nsg-flow-logging-portal.md) (Activer les journaux de flux).

Pour en savoir plus sur la journalisation du groupe de sécurité réseau, consultez [Analyse de journaux pour les groupes de sécurité réseau (NSG)](../virtual-network/virtual-network-nsg-manage-log.md).

Déterminez si le trafic est autorisé ou refusé sur une machine virtuelle en consultant [Vérifier si le trafic est autorisé ou refusé en direction ou en provenance d’une machine virtuelle avec le composant de vérification des flux IP d’Azure Network Watcher](network-watcher-check-ip-flow-verify-portal.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-overview/figure1.png

