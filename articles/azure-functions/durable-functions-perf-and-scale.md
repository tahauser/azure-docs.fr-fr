---
title: "Performances et mise à l’échelle dans Fonctions durables - Azure"
description: "Introduction à l’extension Fonctions durables pour Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: cc4c643b8d0e8de1b5c38ca7bb1b0193d6b0f05b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Performances et mise à l’échelle dans Fonctions durables (Azure Functions)

Pour optimiser les performances et l’évolutivité, il est important de comprendre les caractéristiques uniques de mise à l’échelle de [Fonctions durables](durable-functions-overview.md).

Pour comprendre le comportement de mise à l’échelle, vous devez également comprendre certaines informations du fournisseur de stockage Azure sous-jacent utilisées par Fonctions durables.

## <a name="history-table"></a>Table d’historique

La table d’historique est une table de stockage Azure qui contient les événements d’historique de toutes les instances d’orchestration. Au fur et à mesure que des instances sont exécutées, de nouvelles lignes sont ajoutées à cette table. La clé de partition de la table provient de l’ID d’instance de l’orchestration. Ces valeurs sont aléatoires dans la plupart des cas, ce qui garantit une distribution optimale des partitions internes dans le stockage Azure.

## <a name="internal-queue-triggers"></a>Déclencheurs de file d’attente interne

Les fonctions d’orchestrateur et les fonctions d’activité sont déclenchées par des files d’attente internes dans le compte de stockage par défaut de l’application de fonction. Il existe deux types de file d’attente dans Fonctions durables : la **file d’attente de contrôle** et la **file d’attente des éléments de travail**.

### <a name="the-work-item-queue"></a>File d’attente des éléments de travail

Il existe une file d’attente des éléments de travail par hub de tâches dans Fonctions durables. Il s’agit d’une file d’attente de base qui se comporte comme toute autre file d’attente `queueTrigger` dans Azure Functions. Cette file d’attente sert à déclencher des *fonctions d’activité* sans état. Quand une application Fonctions durables est étendue à plusieurs machines virtuelles, ces dernières rivalisent entre elles pour acquérir le travail de la file d’attente des éléments de travail.

### <a name="control-queues"></a>File(s) d’attente de contrôle

La *file d’attente de contrôle* est plus sophistiquée et complexe que la file d’attente des éléments de travail. Elle sert à déclencher les fonctions d’orchestrateur avec état. Étant donné que les instances de fonction d’orchestrateur sont des singletons avec état, il est impossible d’utiliser un modèle de consommateur concurrent pour distribuer la charge sur les machines virtuelles. À la place, la charge des messages d’orchestrateur est équilibrée sur plusieurs files d’attente de contrôle. Vous trouverez plus d’informations à ce sujet dans les sections suivantes.

Les files d’attente de contrôle contiennent différents types de message couvrant le cycle de vie de l’orchestration, tels que des [messages de contrôle d’orchestrateurs](durable-functions-instance-management.md), des messages de *réponse* de fonctions d’activité et des messages de minuteurs.

## <a name="orchestrator-scale-out"></a>Mise à l’échelle de l’orchestrateur

Les fonctions d’activité sont sans état et automatiquement mises à l’échelle en ajoutant des machines virtuelles. Les fonctions d’orchestrateur sont, quant à elles, *partitionnées* sur une ou plusieurs files d’attente de contrôle. Le nombre de files d’attente de contrôle est fixe et ne peut pas être modifié après avoir initié la création de la charge.

Durant la mise à l’échelle sur plusieurs instances de l’hôte de fonction (généralement sur différentes machines virtuelles), chaque instance acquiert un verrou sur l’une des files d’attente de contrôle. Ce verrou permet de s’assurer qu’une instance d’orchestration ne s’exécute que sur une machine virtuelle à la fois. Cela signifie que si un hub de tâches est configuré avec trois files d’attente de contrôle, la charge des instances d’orchestration peut être équilibrée sur trois machines virtuelles. Il est possible d’ajouter des machines virtuelles supplémentaires pour augmenter la capacité d’exécution de la fonction d’activité.  Mais les ressources supplémentaires ne seront pas utilisées pour exécuter des fonctions d’orchestrateur.

Le diagramme suivant illustre la façon dont l’hôte Azure Functions interagit avec les entités de stockage dans un environnement mis à l’échelle.

![Diagramme de mise à l’échelle](media/durable-functions-perf-and-scale/scale-diagram.png)

Comme vous pouvez le constater, toutes les machines virtuelles peuvent rivaliser entre elles pour acquérir les messages de la file d’attente des éléments de travail. Toutefois, seules trois machines virtuelles peuvent acquérir les messages des files d’attente de contrôle, et chacune d’elles verrouille une file d’attente de contrôle.

Les instances d’orchestration sont distribuées sur les instances de file d’attente de contrôle en exécutant une fonction de hachage interne sur l’ID d’instance de l’orchestration. Les ID d’instance sont générés automatiquement et aléatoires par défaut pour garantir l’équilibrage des instances sur toutes les files d’attente de contrôle disponibles. Le nombre par défaut de partitions de file d’attente de contrôle prises en charge est actuellement de **4**.

> [!NOTE]
> Il n’est pas encore possible de configurer le nombre de partitions de file d’attente de contrôle dans Azure Functions. [Les efforts menés pour prendre en charge cette option de configuration sont suivis](https://github.com/Azure/azure-functions-durable-extension/issues/73).

En règle générale, les fonctions d’orchestrateur sont conçues pour être légères et elles ne devraient pas nécessiter une grande puissance de calcul. C’est pourquoi il n’est pas nécessaire de créer un grand nombre de partitions de file d’attente de contrôle pour obtenir un haut débit. Au lieu de cela, le travail lourd est principalement effectué dans les fonctions d’activité sans état, qui peuvent être mises à l’échelle à l’infini.

## <a name="auto-scale"></a>Mise à l’échelle automatique

Comme avec toutes les fonctions Azure exécutées dans le plan Consommation, Fonctions durables prend en charge la mise à l’échelle automatique via le [contrôleur de mise à l’échelle Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling). Le contrôleur de mise à l’échelle effectue le monitoring de la longueur de la file d’attente des éléments de travail et de chacune des files d’attente de contrôle, en ajoutant ou supprimant des instances de machine virtuelle en conséquence. Si la longueur des files d’attente de contrôle augmente au fil du temps, le contrôleur de mise à l’échelle continue à ajouter des instances de machine virtuelle jusqu'à ce qu’il ait atteint le nombre de partitions de file d’attente de contrôle. Si la longueur de la file d’attente des éléments de travail augmente au fil du temps, le contrôleur de mise à l’échelle continue à ajouter des instances de machine virtuelle jusqu'à ce qu’il puisse répondre à la charge, quel que soit le nombre de partitions de file d’attente de contrôle.

## <a name="thread-usage"></a>Utilisation de threads

Les fonctions d’orchestrateur sont exécutées sur un seul thread. Cela est indispensable pour garantir que l’exécution d’une fonction d’orchestrateur soit déterministe. Dans cette optique, il est important de ne jamais occuper inutilement le thread de la fonction d’orchestrateur avec des tâches telles que des opérations d’E/S (interdites pour diverses raisons), de blocage ou de bouclage. Tout travail pouvant nécessiter un thread d’E/S ou de blocage ou bien plusieurs threads doit être transféré vers les fonctions d’activité.

Les fonctions d’activité ont les mêmes comportements que les fonctions normales déclenchées par une file d’attente. Cela signifie qu’elles peuvent, en toute sécurité, effectuer des opérations d’E/S ou des opérations gourmandes en ressources de processeur et utiliser plusieurs threads. Étant donné que les déclencheurs d’activité sont sans état, ils peuvent librement être mis à l’échelle sur un nombre illimité de machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Installer l’extension Fonctions durables et des exemples](durable-functions-install.md)
