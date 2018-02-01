---
title: "Mise à niveau vers la version 2 du Kit de développement logiciel (SDK) .NET Management Recherche Azure | Microsoft Docs"
description: "Mise à niveau vers la version 2 du Kit de développement logiciel (SDK) .NET Management Recherche Azure"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: ade32dcb4e0885c251c17fad46fb753b6134d027
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Mise à niveau vers la version 2 du Kit de développement logiciel (SDK) .NET Management Recherche Azure
Si vous utilisez la version 1.0.2 ou une version antérieure du [Kit de développement logiciel (SDK) .NET Management Recherche Azure](https://aka.ms/search-mgmt-sdk), cet article vous aidera à mettre à niveau votre application pour utiliser la version 2.

La version 2 du Kit SDK .NET Management Recherche Azure contient des modifications par rapport aux versions antérieures. Elles sont pour la plupart mineures, et donc, la modification de votre code devrait ne nécessiter que peu d’effort. Consultez la page [Procédure de mise à niveau](#UpgradeSteps) pour obtenir des instructions sur la façon de modifier le code à utiliser avec la nouvelle version du kit de développement logiciel.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Nouveautés de la version 2
La version 2 du Kit SDK .NET Management Recherche Azure cible la même version qui est généralement disponible de l’API REST Management Recherche Azure comme les versions précédentes du Kit SDK, et plus spécifiquement la version 2015-08-19. Les modifications apportées au Kit SDK concernent strictement des modifications côté client visant à améliorer la facilité d’utilisation du Kit SDK en soi. Elles incluent notamment les suivantes :

* `Services.CreateOrUpdate` et ses versions asynchrones interrogent désormais automatiquement le provisionnement `SearchService` et ne retournent aucune donnée avant la fin du provisionnement du service. Cela vous évite d’avoir à écrire vous-même ce code d’interrogation.
* Si vous souhaitez continuer à interroger manuellement le provisionnement du service, vous pouvez utiliser la nouvelle méthode `Services.BeginCreateOrUpdate` ou l’une de ses versions asynchrones.
* De nouvelles méthodes `Services.Update` et leurs versions asynchrones ont été ajoutées au Kit SDK. Ces méthodes utilisent HTTP PATCH pour prendre en charge la mise à jour incrémentielle d’un service. Par exemple, vous pouvez désormais mettre à l’échelle un service en appliquant ces méthodes à une instance `SearchService` contenant uniquement les propriétés `partitionCount` et `replicaCount` souhaitées. L’ancienne manière d’appeler `Services.Get`, qui modifiait l’instance `SearchService` retournée et lui appliquait `Services.CreateOrUpdate`, est toujours prise en charge, mais elle n’est plus nécessaire. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Procédure de mise à niveau
Tout d’abord, mettez à jour vos références NuGet `Microsoft.Azure.Management.Search` , soit en utilisant la console NuGet Package, soit en effectuant un clic droit sur les références de votre projet et en sélectionnant « Gérer les packages NuGet » dans Visual Studio.

Une fois que NuGet a téléchargé les nouveaux packages et leurs dépendances, recompilez votre projet. En fonction de la structure de votre code, la recompilation peut réussir. Si c’est le cas, vous êtes prêt !

Si votre build échoue, cela est peut-être dû au fait que vous avez implémenté certaines des interfaces du Kit SDK (par exemple, à des fins de tests unitaires) qui ont été modifiées. Pour résoudre ce problème, vous devez implémenter les nouvelles méthodes telles que `BeginCreateOrUpdateWithHttpMessagesAsync`.

Une fois que vous avez résolu les erreurs de build, vous pouvez apporter des modifications à votre application pour exploiter les nouvelles fonctionnalités si vous le souhaitez. Les nouvelles fonctionnalités du Kit SDK sont détaillées dans la section [Nouveautés de la version 2](#WhatsNew).

## <a name="conclusion"></a>Conclusion
N’hésitez pas à nous faire part de vos commentaires sur le kit de développement logiciel. Si vous rencontrez des problèmes, n’hésitez pas à nous demander de l’aide sur le [forum Azure Search MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Si vous trouvez un bogue, vous pouvez signaler un problème dans le [Référentiel GitHub du Kit de développement logiciel (SDK) Azure .NET](https://github.com/Azure/azure-sdk-for-net/issues). N’oubliez pas de faire précéder le titre de votre problème du préfixe « [Recherche Azure] ».

Merci d’utiliser Azure Search !
