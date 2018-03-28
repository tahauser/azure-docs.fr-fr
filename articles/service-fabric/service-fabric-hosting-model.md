---
title: Modèle d’hébergement Azure Service Fabric | Microsoft Docs
description: Décrit la relation entre les réplicas (ou instances) d’un service Service Fabric déployé et le processus hôte du service.
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 0206a9a486e3511834a23b3cc3f20f236a1cc261
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-hosting-model"></a>Modèle d’hébergement Service Fabric
Cet article fournit une vue d’ensemble des modèles d’hébergement d’applications fournis par Service Fabric et décrit les différences entre les modèles à **processus partagé** et à **processus exclusif**. Il décrit le fonctionnement d’une application déployée sur un nœud Service Fabric et présente la relation entre les réplicas (ou instances) du service et le processus hôte du service.

Avant de continuer, veillez à vous familiariser avec le [modèle d’application Service Fabric][a1] et à comprendre les différents concepts et leurs relations. 

> [!NOTE]
> Dans cet article, par souci de simplicité et sauf indication contraire :
>
> - Toutes les utilisations du mot *réplica* fait référence à la fois au réplica d’un service avec état et à une instance d’un service sans état.
> - *CodePackage* est traité de la même manière que le processus *ServiceHost*, qui inscrit un *ServiceType* et héberge des réplicas de services du même *ServiceType*.
>

Pour comprendre le modèle d’hébergement, commençons par un exemple. Imaginons que nous avons un *applicationtype* « MyAppType », qui a un *ServiceType* « MyServiceType ».  « MyServiceType » est fourni par le *ServicePackage* « MyServicePackage », qui a un *CodePackage* « MyCodePackage ». « MyCodePackageB » inscrit le *ServiceType* « MyServiceType » au moment de son exécution.

Imaginons que nous avons un cluster à trois nœuds et que nous créons une *application* **fabric:/App1** de type « MyAppType ». À l’intérieur de cette *application* **fabric:/App1**, nous créons un service **fabric:/App1/ServiceA** de type « MyServiceType » qui comporte deux partitions (par exemple, **P1** & **P2**) avec 3 réplicas par partition. Le schéma suivant illustre la vue de cette application lorsqu’elle est déployée sur un nœud.

<center>
![Vue de l’application déployée sur un nœud][node-view-one]
</center>

Service Fabric a activé « MyServicePackage », qui a démarré « MyCodePackage », lequel héberge les réplicas des deux partitions.  Par exemple, **P1** & **P2**. Notez que le cluster aura la même vue puisque nous avons choisi un nombre de réplicas par partition égal au nombre de nœuds dans le cluster. Nous allons maintenant créer un autre service **fabric:/App1/ServiceB** dans l’application **fabric:/App1**, qui comporte une partition (par exemple **P3**), avec trois réplicas pour chaque partition. Le schéma suivant illustre la nouvelle vue sur le nœud :

<center>
![Vue de l’application déployée sur un nœud][node-view-two]
</center>

Comme nous pouvons le constater, Service Fabric a placé le nouveau réplica de la partition **P3** du service **fabric:/App1/ServiceB** dans l’activation existante de « MyServicePackage ». Créons à présent une autre *application* **fabric : / App2** de type « MyAppType ». À l’intérieur de **fabric : / App2**, créez un service **fabric:/App2/ServiceA** qui a deux partitions (par exemple **P4** & **P5**) et trois réplicas par partition. Le schéma suivant illustre la nouvelle vue du nœud :

<center>
![Vue de l’application déployée sur un nœud][node-view-three]
</center>

Service Fabric active une nouvelle copie de « MyServicePackage », qui démarre une nouvelle copie de « MyCodePackage ». Les réplicas des deux partitions du service **fabric:/App2/ServiceA** (par exemple, **P4** & **P5**) sont placés dans cette nouvelle copie « MyCodePackage ».

## <a name="shared-process-model"></a>Modèle à processus partagé
Nous venons de voir le modèle d’hébergement par défaut fourni par Service Fabric, que l’on appelle le modèle à **processus partagé**. Dans ce modèle, pour une *application* donnée, seule une copie d’un *ServicePackage* donné est activée sur un *nœud* (qui démarre tous les *CodePackages* qu’il contient) et tous les réplicas de tous les services d’un *ServiceType* donné sont placés dans le *CodePackage* qui inscrit ce *ServiceType*. En d’autres termes, tous les réplicas de tous les services sur un nœud d’un *ServiceType* donné partagent le même processus.

## <a name="exclusive-process-model"></a>Modèle à processus exclusif
L’autre modèle d’hébergement fourni par Service Fabric est le modèle à **processus exclusif**. Dans ce modèle, sur un *nœud* donné, pour placer chaque réplica, Service Fabric active une nouvelle copie d’un *ServicePackage* (qui démarre tous les *CodePackages* qu’il contient) et le réplica est placé dans le *CodePackage* qui a inscrit le *ServiceType* du service auquel le réplica appartient. En d’autres termes, chaque réplica réside dans son propre processus dédié. 

Ce modèle est pris en charge à partir de la version 5.6 de Service Fabric. Le modèle **Processus exclusif** peut être choisi au moment de la création du service (à l’aide de [PowerShell][p1], [REST][r1] ou [FabricClient][c1]) en spécifiant **ServicePackageActivationMode** en tant que « ExclusiveProcess ».

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Si votre manifeste d’application comporte un service par défaut, vous pouvez choisir le modèle à **processus exclusif** en spécifiant l’attribut **ServicePackageActivationMode** comme indiqué ci-dessous :

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
En poursuivant avec l’exemple précédent, créons maintenant un autre service **fabric:/App1/ServiceC** dans l’application **fabric:/App1** qui comporte deux partitions (par exemple **P6** & **P7**), avec trois réplicas par partition et où **ServicePackageActivationMode** est défini sur « ExclusiveProcess ». Le schéma suivant illustre la nouvelle vue sur le nœud :

<center>
![Vue de l’application déployée sur un nœud][node-view-four]
</center>

Comme vous pouvez le voir, Service Fabric a activé deux nouvelles copies de « MyServicePackage » (une pour chaque réplica des partitions **P6** & **P7**) et placé chaque réplica dans sa copie dédiée de *CodePackage*. Notez également que, lorsque le modèle à **processus exclusif** est utilisé, plusieurs copies d’un *ServicePackage* donné peuvent être actives sur un *nœud* pour une *application* donnée. Dans l’exemple ci-dessus, nous voyons que trois copies de « MyServicePackage » sont actives pour **fabric:/App1**. Chacune de ces copies actives de « MyServicePackage » est associée à un **ServicePackageActivationId** qui identifie cette copie dans *l’application* **fabric:/App1**.

Lorsque seul le modèle à **processus partagé** est utilisé pour une *application*, comme **fabric:/App2** dans l’exemple ci-dessus, il n’existe qu’une seule copie active de *ServicePackage* sur un *nœud* et le **ServicePackageActivationId** de cette activation de *ServicePackage* est une « chaîne vide ».

> [!NOTE]
>- Le modèle d’hébergement à **processus partagé** correspond à un **ServicePackageActivationMode** égal à **SharedProcess**. Il s’agit du modèle d’hébergement par défaut et il n’est pas nécessaire de spécifier **ServicePackageActivationMode** au moment de la création du service.
>
>- Le modèle d’hébergement **Processus exclusif** correspond à **ServicePackageActivationMode** défini sur **ExclusiveProcess** et doit être explicitement spécifié au moment de la création du service. 
>
>- Le modèle d’hébergement d’un service peut être déterminé en interrogeant la [description du service][p2] et en examinant la valeur de **ServicePackageActivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>Utilisation d’un package de services déployé
Une copie active d’un *ServicePackage* sur un nœud est appelée [package de services déployé][p3]. Comme mentionné précédemment, lorsque le modèle **Processus exclusif** est utilisé pour la création de services, il peut y avoir, pour une *application* donnée, plusieurs packages de services déployés pour le même *ServicePackage*. Lorsque vous exécutez des opérations spécifiques sur un package de services déployé (par exemple lorsque [vous générez des rapports d’intégrité pour un package de services déployé][p4] ou que [vous redémarrez le package de code d’un package de services déployé][p5]), **ServicePackageActivationId** doit être fourni pour identifier un package de services déployé spécifique.

Le **ServicePackageActivationId** d’un package de services déployé peut être obtenu en interrogeant la liste des [packages de services déployés][p3] sur un nœud. Lorsque vous interrogez les [types de services déployés][p6], les [réplicas déployés][p7] et les [packages de code déployés][p8] sur un nœud, le résultat de la requête contient également le **ServicePackageActivationId** du package de service parent déployé.

> [!NOTE]
>- Sous le modèle d’hébergement à **processus partagé**, une seule copie d’un *ServicePackage* est activée sur un *nœud* donné et pour une *application* donnée. Le **ServicePackageActivationId** est alors égal à une *chaîne vide* et il n’est pas nécessaire de le spécifier lors de l’exécution des opérations associées au package de services déployé. 
>
> - Sous le modèle d’hébergement à **processus exclusif**, une ou plusieurs copies d’un *ServicePackage* peuvent être actives sur un *nœud* donné et pour une *application* donnée. Chaque activation est associée à un *ServicePackageActivationId* **non vide** spécifié lors de l’exécution des opérations associées au package de services déployé. 
>
> - Si le **ServicePackageActivationId** est omis, la valeur par défaut sera une « chaîne vide ». En présence d’un package de services déployé qui a été activé sous le modèle à **processus partagé**, l’opération sera exécutée sur ce serveur ; sinon l’opération échouera.
>
> - Il n’est pas recommandé d’interroger puis de mettre en cache le **ServicePackageActivationId** car il est généré dynamiquement et peut changer pour différentes raisons. Avant d’effectuer une opération qui requiert un **ServicePackageActivationId**, vous devez commencer par interroger la liste des [packages de services déployés][p3] sur un nœud, puis utiliser le **ServicePackageActivationId** généré à partir du résultat de la requête pour exécuter l’opération d’origine.
>
>

## <a name="guest-executable-and-container-applications"></a>Applications d’exécutables invités et de conteneurs
Service Fabric traite les applications [d’exécutable invité][a2] et de [conteneur][a3] en tant que services sans état autonome ; autrement dit, il n’y a aucun runtime Service Fabric dans *ServiceHost* (un processus ou un conteneur). Étant donné que ces services sont autonomes, le nombre de réplicas par *ServiceHost* n’est pas applicable à ces services. La configuration la plus couramment utilisée avec ces services est la partition unique avec un [InstanceCount][c2] égal à -1 (autrement dit, une seule copie du code de service est en cours d’exécution sur chaque nœud du cluster). 

Le **ServicePackageActivationMode** par défaut pour ces services est **SharedProcess** ; dans ce cas, Service Fabric active uniquement une copie de *ServicePackage* sur un *Nœud* pour une *application* donnée.  Cela signifie qu’une seule copie du code de service exécutera un *Nœud*. Si vous souhaitez que plusieurs copies de votre code de service s’exécutent sur un *nœud* lorsque vous créez plusieurs services (*Service1* à *ServiceN*) d’un *ServiceType* (spécifié dans *ServiceManifest*) ou lorsque votre service comporte plusieurs partitions, vous devez spécifier **ServicePackageActivationMode** en tant que **ExclusiveProcess** au moment de la création du service.

## <a name="changing-hosting-model-of-an-existing-service"></a>Modification du modèle d’hébergement d’un service existant
Le mécanisme de mise à niveau ou de mise à jour (ou la spécification du service par défaut dans le manifeste d’application) ne permet pas actuellement de passer, pour un service existant, d’un modèle d’hébergement à **processus partagé** vers un modèle d’hébergement à **processus exclusif** et inversement. Cette fonctionnalité sera prise en charge dans les futures versions.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Choix entre un modèle à processus partagé et un modèle à processus exclusif
Ces deux modèles d’hébergement présentent des avantages et des inconvénients. Il est donc important que l’utilisateur détermine le modèle qui répond le mieux à ses besoins. Le modèle à **processus partagé** garantit une meilleure utilisation des ressources du système d’exploitation, puisqu’il réduit le nombre de processus générés, permet à plusieurs réplicas du même processus de partager des ports, etc. Toutefois, si l’un des réplicas rencontre une erreur qui l’oblige à arrêter l’hôte de service, cela aura un impact sur tous les autres réplicas du même processus.

 Le modèle à **processus exclusif** isole mieux les réplicas en les associant à un processus dédié. Aussi, un dysfonctionnement d’un réplica n’aura aucune incidence sur les autres réplicas. Ce modèle est utile lorsque le partage de port n’est pas pris en charge par le protocole de communication. Il permet également d’appliquer la gouvernance des ressources au niveau du réplica. En revanche, **Processus exclusif** consomme plus de ressources du système d’exploitation puisqu’il engendre un processus pour chaque réplica présent sur le nœud.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Considérations relatives au modèle à processus exclusif et au modèle d’application
Pour modéliser votre application dans Service Fabric, il est recommandé de conserver un seul *ServiceType* par *ServicePackage*. Ce modèle fonctionne très bien pour la plupart des applications. 

Dans certains cas d’usage, Service Fabric autorise également plusieurs *ServiceType* par *ServicePackage* (et un *CodePackage* peut inscrire plusieurs *ServiceType*). Voici quelques scénarios dans lesquels ces configurations peuvent être utiles :

- Vous souhaitez optimiser l’utilisation des ressources du système d’exploitation en générant moins de processus et en augmentant la densité de réplica par processus.
- Les réplicas de différents ServiceTypes doivent partager certaines données qui présentent un coût élevé d’initialisation ou de mémoire.
- Vous disposez d’une offre de service gratuite et vous souhaitez limiter l’utilisation des ressources en plaçant tous les réplicas du service dans le même processus.

Le modèle d’hébergement à **processus exclusif** n’est pas cohérent avec le modèle d’application qui utilise plusieurs *ServiceTypes* par *ServicePackage*. En effet, plusieurs *ServiceTypes* par *ServicePackage* sont destinés à améliorer le partage des ressources entre les réplicas et à augmenter la densité de réplica par processus. Cette utilisation est contraire à l’intention du modèle à **processus exclusif**.

Prenons le cas de plusieurs *ServiceTypes* par *ServicePackage* avec un *CodePackage* différent qui inscrit chaque *ServiceType*. Supposons que nous avons un *ServicePackage* « MultiTypeServicePackage » qui comporte deux *CodePackages* :

- « MyCodePackageA », qui inscrit le *ServiceType* « MyServiceTypeA ».
- « MyCodePackageB », qui inscrit le *ServiceType* « MyServiceTypeB ».

Supposons, à présent, que nous créons une *application* **fabric:/SpecialApp** et que, à l’intérieur de cette application **fabric:/SpecialApp**, nous créons les deux services suivants avec le modèle à **processus exclusif** :

- Le service **fabric:/SpecialApp/ServiceA** de type « MyServiceTypeA » avec deux partitions (par exemple, **P1** et **P2**) et trois réplicas par partition.
- Le service **fabric:/SpecialApp/ServiceB** de type « MyServiceTypeB » avec deux partitions (par exemple, **P3** et **P4**) et trois réplicas par partition.

Sur un nœud donné, les deux services auront chacun deux réplicas. Étant donné que nous avons utilisé le modèle à **processus exclusif** pour créer les services, Service Fabric activera une nouvelle copie de « MyServicePackage » pour chaque réplica. Chaque activation de «MultiTypeServicePackage » démarre une copie de « MyCodePackageA » et « MyCodePackageB ». Cependant, le réplica pour lequel « MultiTypeServicePackage » a été activé ne pourra être hébergé que par « MyCodePackageA » ou par « MyCodePackageB ». Le schéma qui suit présente la vue du nœud :

<center>
![Vue de l’application déployée sur un nœud][node-view-five]
</center>

Dans l’activation de « MultiTypeServicePackage » pour le réplica de la partition **P1** du service **fabric:/SpecialApp/ServiceA**, « MyCodePackageA » héberge le réplica et « MyCodePackageB » est opérationnel. De même, dans l’activation de « MultiTypeServicePackage » pour le réplica de la partition **P3** du service **fabric:/SpecialApp/ServiceB**, « MyCodePackageB » héberge le réplica et « MyCodePackageA » est simplement en cours d’exécution, et ainsi de suite. Par conséquent, plus il y a de *CodePackages* (inscrivant différents *ServiceTypes*) par *ServicePackage*, plus on utilisera de ressources redondantes. 
 
 En revanche, si nous créons les services **fabric:/SpecialApp/ServiceA** et **fabric:/SpecialApp/ServiceB** avec le modèle à **Processus partagé**, Service Fabric activera une seule copie de « MultiTypeServicePackage » pour *l’application* **fabric:/SpecialApp** (comme nous l’avons vu précédemment). « MyCodePackageA » héberge tous les réplicas de service **fabric:/SpecialApp/ServiceA** (ou de n’importe quel service de type « MyServiceTypeA » pour être plus précis). « MyCodePackageB » héberge tous les réplicas de service **fabric:/SpecialApp/ServiceB** (ou de n’importe quel service de type « MyServiceTypeB » pour être plus précis). Le schéma qui suit présente la vue du nœud dans ce paramètre : 

<center>
![Vue de l’application déployée sur un nœud][node-view-six]
</center>

Dans l’exemple précédent, vous pourriez penser que si « MyCodePackageA » inscrit « MyServiceTypeA » et « MyServiceTypeB » et s’il n’existe aucun « MyCodePackageB », aucun *CodePackage* redondant n’est exécuté. Cela est correct, toutefois, comme mentionné précédemment, ce modèle d’application n’est pas aligné sur le modèle d’hébergement **Processus exclusif**. Si l’objectif est de placer chaque réplica dans son propre processus dédié, alors il n’est pas nécessaire d’inscrire les deux *ServiceTypes* du même *CodePackage*. Il est plus judicieux de placer chaque *ServiceType* dans son propre *ServicePacakge*.

## <a name="next-steps"></a>Étapes suivantes
[Empaquetez une application][a4] et préparez-la pour le déploiement.

[Déployer et supprimer des applications][a5] explique comment utiliser PowerShell pour gérer des instances d’application.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage
