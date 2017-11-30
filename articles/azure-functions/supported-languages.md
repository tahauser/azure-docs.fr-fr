---
title: Langages pris en charge dans Azure Functions
description: "Découvrez les langages qui sont pris en charge (Disponibilité générale) et ceux qui sont en version expérimentale ou en préversion."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="supported-languages-in-azure-functions"></a>Langages pris en charge dans Azure Functions

Cet article explique les niveaux de prise en charge offerts pour les langages que vous pouvez utiliser avec Azure Functions.

## <a name="levels-of-support"></a>Niveaux de prise en charge

Il existe trois niveaux de prise en charge :

* **Disposition générale (GA)** : entièrement pris en charge et approuvé pour la production.
* **Préversion** : pas encore pris en charge, mais le statut de disponibilité générale est prévu à l’avenir.
* **Expérimental** : pas pris en charge et peut être abandonné à l’avenir. Aucune garantie quant à une éventuelle préversion ou un passage à l’état de disponibilité générale.

## <a name="languages-in-runtime-1x-and-2x"></a>Langages dans les runtimes 1.x et 2.x

[Deux versions du runtime Azure Functions](functions-versions.md) sont disponibles. Le runtime 1.x est en disponibilité générale. Il s’agit du seul runtime approuvé pour les applications de production. Le runtime 2.x est actuellement en préversion. Les langages qu’il prend en charge sont donc en préversion. Le tableau suivant montre les langages qui sont pris en charge dans chaque version du runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Langages expérimentaux

Les langages expérimentaux dans la version 1.x ne sont pas très propices à la mise à l’échelle et ne prennent pas en charge toutes les liaisons. Par exemple, Python est lent car le runtime Functions exécute *python.exe* à chaque appel de fonction. Et bien que Python prenne en charge les liaisons HTTP, il ne peut pas accéder à l’objet de requête.

La prise en charge expérimentale pour PowerShell est limitée à la version 4.0, car c’est ce qui est installé sur les machines virtuelles sur lesquelles les applications Function s’exécutent. Si vous souhaitez exécuter des scripts PowerShell, utilisez [Azure Automation](https://azure.microsoft.com/services/automation/).

Le runtime 2.x ne prend pas en charge les langages expérimentaux. Dans la version 2.x, nous ajouterons la prise en charge d’un langage uniquement quand il est évolutif et prend en charge des déclencheurs avancés.

Si vous souhaitez utiliser l’un des langages disponibles uniquement dans la version 1.x, conservez le runtime 1.x. Mais, n’utilisez pas de langages expérimentaux pour tout ce qui est essentiel, car il n’existe aucune prise en charge officielle pour eux. Vous pouvez demander de l’aide en [créant des problèmes GitHub](https://github.com/Azure/azure-webjobs-sdk-script/issues), mais les cas de support technique ne doivent pas être ouverts pour des problèmes liés aux langages expérimentaux. 

### <a name="language-extensibility"></a>Extensibilité de langage

Le runtime 2.x est conçu pour offrir une [extensibilité de langage](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Parmi les premiers langages basés sur ce modèle d’extensibilité figure Java, qui est en Préversion dans le runtime 2.x.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la façon d’utiliser l’un des langages GA ou Préversion dans Azure Functions, consultez les ressources suivantes :

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)