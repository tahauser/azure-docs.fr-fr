---
title: Induire un chaos dans les clusters Service Fabric | Microsoft Docs
description: "Utilisation des API du service d’injection d’erreurs et d’analyse du cluster pour gérer le chaos dans le cluster."
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/10/2017
ms.author: motanv
ms.openlocfilehash: 9a205d1b8e088b7007bb8c3a64139732d8858267
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Induire un chaos contrôlé dans les clusters Service Fabric
Les systèmes distribués à grande échelle, comme les infrastructures cloud, sont par définition peu fiables. Azure Service Fabric permet aux développeurs d’écrire des services distribués fiables sur une infrastructure peu fiable. Pour écrire des services distribués robustes sur une infrastructure non fiable, les développeurs doivent pouvoir tester la stabilité de leurs services, tandis que l’infrastructure sous-jacente non fiable passe par des transitions d’état complexes en raison d’erreurs.

Le [service d’injection d’erreurs et d’analyse de cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (ou Service d’analyse des erreurs) permet aux développeurs de provoquer des erreurs afin de tester les services. Ces erreurs simulées ciblées, comme [le redémarrage d’une partition](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), peuvent vous aider à simuler les transitions d’état les plus courantes. Toutefois, les erreurs simulées ciblées sont biaisées par définition, et peuvent donc manquer des bogues qui se présentent uniquement dans une séquence longue, compliquée et difficile à prédire de transitions d’état. Pour un test non biaisé, vous pouvez utiliser Chaos.

Au sein du cluster, le chaos simule des erreurs entrelacées et périodiques, avec et sans perte de données, sur des périodes prolongées. Une erreur sans perte de données se compose d’un ensemble d’appels d’API Service Fabric. Par exemple, une erreur de redémarrage du réplica est une erreur sans perte de données puisqu’il s’agit d’une fermeture suivie d’une ouverture sur un réplica. La suppression du réplica, le déplacement du réplica principal et le déplacement du réplica secondaire sont les autres erreurs sans perte de données exercées par Chaos. Les erreurs avec perte de données sont les sorties de processus, comme les packages de redémarrage du nœud et du code. 

Une fois que vous avez configuré Chaos avec la fréquence et le type des erreurs, vous pouvez le démarrer avec l’API REST, C# ou PowerShell pour générer des erreurs dans le cluster et dans vos services. Vous pouvez configurer Chaos pour qu’il s’exécute pendant une période donnée (par exemple une heure), après quoi Chaos s’arrête automatiquement, ou vous pouvez appeler l’API StopChaos (C#, Powershell ou REST) pour l’arrêter à tout moment.

> [!NOTE]
> Dans sa forme actuelle, le chaos déclenche uniquement des erreurs sécurisées, ce qui implique qu’en l’absence d’erreurs externes, une perte de quorum ou une perte de données ne se produit jamais.
>

Pendant que le chaos s’exécute, il génère différents événements qui capturent l’état de l’exécution à un moment précis. Par exemple, un ExecutingFaultsEvent contient toutes les erreurs que Chaos a choisi d’exécuter dans cette itération. Un ValidationFailedEvent contient les détails d’un échec de validation (problèmes d’intégrité ou de stabilité) qui a été identifié lors de la validation du cluster. Vous pouvez appeler l’API GetChaosReport (C#, Powershell ou REST) pour obtenir un rapport sur les exécutions de Chaos. Ces événements sont rendus persistants dans un [dictionnaire fiable](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), qui a une stratégie de troncation dictée par deux configurations : **MaxStoredChaosEventCount** (la valeur par défaut est 25 000) et **StoredActionCleanupIntervalInSeconds** (la valeur par défaut est 3 600). Toutes les vérifications *StoredActionCleanupIntervalInSeconds* de Chaos et tous les événements *MaxStoredChaosEventCount* à l’exception des plus récents sont purgés du dictionnaire fiable.

## <a name="faults-induced-in-chaos"></a>Erreurs introduites dans le chaos
Le chaos génère des erreurs dans l’ensemble du cluster Service Fabric et compresse, en quelques heures, les erreurs constatées au cours de plusieurs mois ou années. L’utilisation d’erreurs entrelacées avec un taux élevé d’erreurs permet d’identifier des dysfonctionnements qui ne pourraient peut-être pas être isolés autrement. Cet exercice du chaos entraîne une amélioration significative de la qualité du code du service.

Le chaos introduit des erreurs à partir des catégories suivantes :

* Redémarrer un nœud
* Redémarrer un package de code déployé
* Supprimer un réplica
* Redémarrer un réplica
* Déplacer un réplica principal (configurable)
* Déplacer un réplica secondaire (configurable)

Le chaos s’exécute dans de nombreuses itérations. Chaque itération consiste en des validations de cluster et des erreurs pendant la période spécifiée. Vous pouvez configurer les délais de stabilisation du cluster et de validation. Si une défaillance est trouvée dans la validation du cluster, le chaos génère et fait persister un événement ValidationFailedEvent avec l’horodatage UTC et les détails de la défaillance. Prenons, par exemple, une instance de chaos définie pour s’exécuter une heure, avec un maximum de trois erreurs simultanées. Le chaos introduit trois erreurs, puis valide l’intégrité du cluster. Il effectue une itération sur l’étape précédente jusqu'à ce qu’il soit explicitement arrêté par le biais de l’API StopChaosAsync ou jusqu’à ce qu’une heure se soit écoulée. Si le cluster présente un défaut d’intégrité dans l’une des itérations (c’est-à-dire qu’il n’est ni stabilisé ni sain dans le délai MaxClusterStabilizationTimeout passé), Chaos génère un ValidationFailedEvent. Cet événement indique qu’une erreur est survenue et qu’un examen approfondi est peut-être nécessaire.

Pour obtenir les erreurs induites par Chaos, vous pouvez utiliser l’API GetChaosReport (PowerShell, C# ou REST). L’API obtient le segment suivant du rapport Chaos sur la base du jeton de continuation passé ou l’intervalle de temps passé. Vous pouvez soit spécifier le ContinuationToken pour obtenir le segment suivant du rapport Chaos soit spécifier l’intervalle de temps entre StartTimeUtc et EndTimeUtc, mais vous ne pouvez pas spécifier le ContinuationToken et l’intervalle de temps dans le même appel. Lorsqu’il y a plus de 100 événements Chaos, le rapport Chaos est retourné dans les segments ne contenant pas plus de 100 événements Chaos.

## <a name="important-configuration-options"></a>Options de configuration importantes
* **TimeToRun** : durée totale d’exécution du chaos jusqu’à sa fin. Vous pouvez arrêter le chaos avant la fin de la période TimeToRun définie par le biais de l’API StopChaos.

* **MaxClusterStabilizationTimeout**: délai maximal nécessaire à la restauration de l’intégrité du cluster avant de produire un ValidationFailedEvent. Cette attente permet de réduire la charge sur le cluster pendant qu’il récupère. Les vérifications effectuées sont les suivantes :
  * Si l’intégrité du cluster est OK
  * Si l’intégrité du service est OK
  * Si la taille du jeu de réplicas cible est atteinte pour la partition de service
  * Aucun réplica InBuild n’existe
* **MaxConcurrentFaults** : nombre maximal d’erreurs introduites simultanément dans chaque itération. Plus le nombre est élevé, plus Chaos est agressif et plus les basculements et combinaisons de transition d’état qui le cluster traverse sont complexes. 

> [!NOTE]
> Le chaos garantit qu’aucune perte de quorum ou de données ne sera à déplorer en l’absence d’erreurs externes, quelle que soit la valeur de *MaxConcurrentFaults*.
>

* **EnableMoveReplicaFaults** : active ou désactive les erreurs provoquant le déplacement des réplicas primaires ou secondaires. Ces erreurs sont désactivées par défaut.
* **WaitTimeBetweenIterations** : la durée d’attente entre les itérations. Autrement dit, la durée de la pause de Chaos après avoir exécuté une série d’erreurs et terminé la validation d’intégrité de cluster correspondante. Plus la valeur est élevée, plus le taux d’injection d’erreurs moyen est faible.
* **WaitTimeBetweenFaults** : délai d’attente entre deux erreurs consécutives dans une seule itération. Plus la valeur est élevée, plus la simultanéité (ou le chevauchement) des erreurs est faible.
* **ClusterHealthPolicy** : la stratégie de contrôle d’intégrité du cluster est utilisée pour valider l’intégrité du cluster entre les itérations de Chaos. Si l’intégrité du cluster est erronée ou si une exception inattendue se produit pendant l’exécution d’erreurs, Chaos attendra 30 minutes avant le prochain contrôle d’intégrité - pour donner au cluster un temps de récupération.
* **Contexte** : une collection de paires clé-valeur de type (string, string). La carte peut être utilisée pour enregistrer des informations relatives à l’exécution de Chaos. Il ne peut pas y avoir plus de 100 de ces paires et chaque chaîne (clé ou valeur) peut comporter au maximum 4095 caractères. Ce mappage est défini par le démarrage de l’exécution de Chaos afin d’éventuellement stocker le contexte de l’exécution spécifique.

## <a name="how-to-run-chaos"></a>Procédure d’exécution du chaos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };
            
            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}

```
