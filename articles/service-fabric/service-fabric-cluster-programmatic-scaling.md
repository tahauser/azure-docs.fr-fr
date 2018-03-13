---
title: "Mise à l’échelle d’Azure Service Fabric par programmation | Microsoft Docs"
description: "Faire évoluer un cluster Azure Service Fabric par programmation, selon des déclencheurs personnalisés"
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: bfa020e29a9bb67f0634d220725bc11279e1565c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Mettre à l’échelle un cluster Service Fabric par programmation 

Les principes de base de mise à l’échelle d’un cluster Service Fabric dans Azure sont décrits dans la documentation sur la [mise à l’échelle du cluster](./service-fabric-cluster-scale-up-down.md). Cet article décrit comment les clusters Service Fabric s’appuient sur des groupes de machines virtuelles identiques et peuvent être mis à l’échelle manuellement ou avec des règles automatiques. Ce document examine les méthodes de programmation pour coordonner les opérations de mise à l’échelle Azure dans les scénarios plus avancés. 

## <a name="reasons-for-programmatic-scaling"></a>Motifs d’une mise à l’échelle par programmation
Dans de nombreux scénarios, la mise à l’échelle manuelle ou via des règles automatiques est une bonne solution. Cependant, dans d’autres cas de figure, ces deux possibilités ne sont pas indiquées. Les inconvénients potentiels de ces approches sont les suivants :

- La mise à l’échelle manuelle vous oblige à ouvrir une session et à demander explicitement des opérations de mise à l’échelle. Si les opérations de mise à l’échelle sont requises fréquemment ou à des moments imprévisibles, cette approche n’est probablement pas la bonne.
- Lorsque les règles de mise à l’échelle automatique suppriment une instance d’un groupe de machines virtuelles identiques, elles ne suppriment pas automatiquement les connaissances de ce nœud du cluster Service Fabric associé, sauf si le nœud a un niveau de durabilité agent ou or. Comme les règles de mise à l’échelle automatique fonctionnent au niveau du groupe de machines virtuelles (et non au niveau de Service Fabric), elles peuvent supprimer des nœuds Service Fabric sans les arrêter en douceur. Cette suppression brutale laisse l’état de nœud Service Fabric « ghost » derrière elle, après les opérations de mise à l’échelle. Une personne (ou un service) doit nettoyer régulièrement l’état du nœud supprimé dans le cluster Service Fabric.
  - Un type de nœud ayant un niveau de durabilité argent ou or nettoie automatiquement les nœuds supprimés. Par conséquent, aucun nettoyage supplémentaire n’est requis.
- Bien que les règles de mise à l’échelle automatique prennent en charge [plusieurs mesures](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md), ce groupe reste limité. Si votre scénario requiert une mise à l’échelle en fonction d’une mesure non incluse dans ce groupe, les règles de mise à l’échelle automatique ne sont peut-être pas la bonne solution.

Face à ces limitations, vous pouvez mettre en œuvre des modèles personnalisés de mise à l’échelle automatique. 

## <a name="scaling-apis"></a>API de mise à l’échelle
Il existe des API Azure qui permettent aux applications de travailler par programmation avec les groupes de machines virtuelles identiques et des clusters Service Fabric. Si les options de mise à l’échelle automatique existantes ne conviennent pas à votre scénario, ces API permettent de mettre en œuvre une logique de mise à l’échelle personnalisée. 

Cette fonctionnalité de mise à l’échelle automatique personnalisée permet d’ajouter un service sans état à l’application Service Fabric pour gérer les opérations de mise à l’échelle. Dans la méthode `RunAsync` du service, un ensemble de déclencheurs peut déterminer si la mise à l’échelle est requise (avec vérification de paramètres tels que la taille maximale des clusters et de mise à l’échelle de recharges).   

L’API utilisée pour les interactions des groupes de machines virtuelles identiques (afin de vérifier et de modifier le nombre actuel d’instances de machines virtuelles) est la [bibliothèque Fluent Azure Management Compute](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Cette bibliothèque de calcul fluide fournit une API conviviale pour interagir avec les groupes de machines virtuelles identiques.

Pour interagir avec le cluster Service Fabric, utilisez [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Bien sûr, le code de mise à l’échelle n’a pas besoin de s’exécuter comme un service dans le cluster à mettre à l’échelle. `IAzure` et `FabricClient` peuvent se connecter à leurs ressources Azure associées à distance. Donc, le service de mise à l’échelle peut être une application console ou un service Windows exécuté hors de l’application Service Fabric. 

## <a name="credential-management"></a>Gestion des informations d’identification
Un service chargé de gérer la mise à l’échelle doit pouvoir accéder aux ressources d’un groupe de machines virtuelles identiques, sans ouverture de session interactive. L’accès au cluster Service Fabric est simple si le service de mise à l’échelle modifie sa propre application Service Fabric, mais des informations d’identification sont nécessaires pour accéder au groupe identique. Pour vous connecter, vous pouvez utiliser un [principal de service](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) créé avec [Azure CLI 2.0](https://github.com/azure/azure-cli).

Pour créer un principal de service, procédez comme suit :

1. Connectez-vous à l’interface Azure CLI (`az login`) en tant qu’utilisateur ayant accès au groupe de machines virtuelles identiques.
2. Créez le principal de service avec `az ad sp create-for-rbac`
    1. Notez l’ID d’application (appelé ID client ailleurs), le nom, le mot de passe et le client en vue d’une utilisation ultérieure.
    2. Vous aurez également besoin de votre ID d’abonnement, que vous pouvez afficher avec `az account list`.

La bibliothèque de calcul Fluent peut se connecter avec ces informations d’identification de la façon suivante (notez que les principaux types Azure Fluent, par exemple `IAzure`, se trouvent dans le package [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/)) :

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Une fois celle-ci connectée, le nombre d’instances de groupes identiques peut être interrogée avec `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Montée en charge
Le Kit de développement logiciel (SDK) Azure Compute permet d’ajouter des instances au groupe de machines virtuelles identiques, moyennant quelques appels seulement.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Il est également possible de gérer la taille des groupes de machines virtuelles identiques avec des applets de commande PowerShell. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) peut récupérer l’objet groupe de machines virtuelles identiques. La capacité actuelle peut être connue par le biais de la propriété `.sku.capacity`. Une fois la capacité fixée à la valeur souhaitée, il est possible de mettre à jour le groupe de machines virtuelles identiques dans Azure avec la commande [`Update-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

Lorsque vous ajoutez manuellement un nœud, l’ajout d’une instance de groupe identique doit suffire pour démarrer un nouveau nœud Service Fabric, car le modèle de groupe identique contient des extensions pour ajouter automatiquement des instances au cluster Service Fabric. 

## <a name="scaling-in"></a>Mise à l'échelle

La mise à l’échelle est similaire à la montée en charge. Les modifications d’un groupe de machines virtuelles identiques sont pratiquement identiques. Mais, comme nous l’avons déjà vu, Service Fabric ne nettoie automatiquement que les nœuds supprimés ayant une durabilité de niveau or ou argent. Par conséquent, dans le cas d’une mise à l’échelle avec une durabilité de niveau bronze, il est nécessaire d’interagir avec le cluster Service Fabric pour arrêter le nœud à supprimer, puis de supprimer son état.

La préparation du nœud à l’arrêt consiste à trouver le nœud à supprimer (la dernière instance de groupe de machines virtuelles identiques ajoutée) et à le désactiver. Les instances de groupe de machines virtuelles identiques sont numérotées dans l’ordre dans lequel elles sont ajoutées. Ainsi, vous pouvez trouver les nœuds les plus récents en comparant le suffixe du numéro dans les noms des nœuds (qui correspondent aux noms des instances de groupe de machines virtuelles identiques sous-jacents). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Une fois le nœud à supprimer trouvé, il peut être désactivé et supprimé à l’aide de la même instance `FabricClient` et de l’instance `IAzure` précédente.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Comme pour la montée en charge, des cmdlets e PowerShell de modification de la capacité des groupes de machines virtuelles identiques peuvent également être utilisées ici si une approche par scripts est préférable. Une fois l’instance de machine virtuelle supprimée, l’état du nœud Service Fabric peut être supprimé.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="potential-drawbacks"></a>Inconvénients potentiels

Comme nous l’avons vu dans les extraits de code précédents, créer votre propre service de mise à l’échelle optimise le niveau de contrôle et de personnalisation du comportement de mise à l’échelle de votre application. Cela peut être utile dans les cas qui requièrent un contrôle précis sur le moment ou les modalités de la mise à l’échelle ou la montée en charge d’une application. Toutefois, ce contrôle complique le code. Cette approche signifie que vous devez posséder votre propre code de mise à l’échelle, ce qui n’est pas négligeable.

Le mode de mise à l’échelle de Service Fabric dépend de votre cas de figure. Si la mise à l’échelle est rare, la possibilité d’ajouter ou de supprimer des nœuds manuellement est probablement suffisante. Dans les scénarios plus complexes, les règles de mise à l’échelle automatique et les kits de développement logiciel (SDK) qui assurent la mise à l’échelle par programmation offrent des alternatives puissantes.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à implémenter votre propre logique de mise à l’échelle automatique, vous devez vous familiariser avec les API utiles et les concepts suivants :

- [Augmenter ou diminuer la taille des instances d’un cluster Service Fabric à l’aide de règles de mise à l’échelle automatique](./service-fabric-cluster-scale-up-down.md)
- [Bibliothèques Azure Management fluides pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (utiles pour interagir avec les groupes de machines virtuelles identiques sous-jacents d’un cluster Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (utile pour interagir avec un cluster Service Fabric et ses nœuds)
