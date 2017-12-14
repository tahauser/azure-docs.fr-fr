---
title: "Diffusion des données d’Azure Diagnostics dans le chemin réactif à l’aide d’Event Hubs | Microsoft Docs"
description: "Configuration de bout en bout d’Azure Diagnostics avec Event Hubs, y compris des conseils relatifs aux scénarios courants."
services: event-hubs
documentationcenter: na
author: rboucher
manager: carmonm
editor: 
ms.assetid: edeebaac-1c47-4b43-9687-f28e7e1e446a
ms.service: monitoring-and-diagnostics
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: robb
ms.openlocfilehash: ca0dd96389a605ed8bf34af81eb4d75bef581338
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Diffusion des données d’Azure Diagnostics dans le chemin réactif à l’aide d’Event Hubs
Azure Diagnostics propose des moyens flexibles de collecter des mesures et des journaux à partir de machines virtuelles de services cloud et de transférer les résultats dans Azure Storage. Depuis mars 2016 (Kit de développement logiciel (SDK) 2.9), vous pouvez envoyer les données Diagnostics à des sources de données personnalisées et transférer des données de chemin réactif en quelques secondes à l’aide [d’Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Les types de données pris en charge sont les suivants :

* Suivi d’événements pour les événements Windows (ETW)
* Compteurs de performances
* Journaux d’événements Windows
* Journaux d’application
* Journaux d’infrastructure de diagnostics Azure

Cet article vous montre la procédure complète de configuration de diagnostics  Azure avec Event Hubs. Des recommandations vous sont également proposées pour les scénarios courants suivants :

* Comment personnaliser les journaux et les mesures envoyés à Event Hubs
* Comment modifier les configurations dans chaque environnement
* Comment afficher les données de flux Event Hubs
* Comment résoudre les problèmes de connexion  

## <a name="prerequisites"></a>Composants requis
La réception par Event Hubs de données provenant d’Azure Diagnostics est prise en charge dans Services cloud, Machines virtuelles, Virtual Machine Scale Sets et Service Fabric à partir du Kit de développement logiciel (SDK) 2.9 Azure, ainsi que dans les outils Azure correspondants pour Visual Studio.

* Extension Azure Diagnostics 1.6 (ciblée par défaut par le[Kit de développement logiciel (SDK) Azure pour .NET 2.9 ou ultérieur](https://azure.microsoft.com/downloads/) )
* [Visual Studio 2013 ou une version ultérieure](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Configurations existantes d’Azure Diagnostics dans une application à l’aide d’un fichier *.wadcfgx* et de l’une des méthodes suivantes :
  * Visual Studio : [Configuration de Diagnostics pour les services cloud et les machines virtuelles Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Windows PowerShell : [Activer les diagnostics dans Services cloud Azure à l’aide de PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
* Espace de noms Event Hubs approvisionné tel que décrit dans l’article [Prise en main d’Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Connexion d’Azure Diagnostics au récepteur Event Hubs
Par défaut, Azure Diagnostics transmet toujours des journaux et des mesures à un compte Stockage Azure. Une application peut également envoyer des données vers Event Hubs en ajoutant une nouvelle section **Sinks** sous l’élément **PublicConfig** / **WadCfg** du fichier *.wadcfgx*. Dans Visual Studio, le fichier *.wadcfgx* est stocké dans le chemin suivant : **Projet de service cloud** > **Rôles** > **(RoleName)** > fichier **diagnostics.wadcfgx**.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

Dans cet exemple, l’URL du hub d’événements est définie sur l’espace de noms complet du hub d’événements (espace de noms Event Hubs + « / » + nom du hub d’événements).  

L’URL du hub d’événements s’affiche dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkID=213885) dans le tableau de bord Event Hubs.  

Le nom **Sink** peut être défini sur n’importe quelle chaîne valide tant que cette même valeur est utilisée de manière cohérente dans le fichier de configuration.

> [!NOTE]
> Des récepteurs supplémentaires, tels que *applicationInsights* , peuvent être configurés dans cette section. Azure Diagnostics permet de définir un ou plusieurs récepteurs si chaque récepteur est également déclaré dans la section **PrivateConfig** .  
>
>

Le récepteur Event Hubs doit également être déclaré et défini dans la section **PrivateConfig** du fichier de configuration *.wadcfgx* .

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

La valeur `SharedAccessKeyName` doit correspondre à une clé de signature d’accès partagé (SAS) et à une stratégie définie dans l’espace de noms **Event Hubs** . Dans le [portail Azure](https://portal.azure.com), accédez au tableau de bord Event Hubs, cliquez sur l’onglet **Configurer** et définissez une stratégie nommée (par exemple, « SendRule ») qui possède des autorisations *d’envoi* . L’élément **StorageAccount** est également déclaré dans le fichier **PrivateConfig**. Il est inutile de modifier les valeurs ici si elles fonctionnent correctement. Dans cet exemple, nous ne renseignons pas les valeurs afin d’indiquer que ces valeurs seront définies par une ressource en aval. Par exemple, le fichier de configuration d’environnement *ServiceConfiguration.Cloud.cscfg* définit les noms et les clés appropriés à l’environnement.  

> [!WARNING]
> La clé SAP Event Hubs est stockée en texte brut dans le fichier *.wadcfgx*. Cette clé est souvent intégrée au contrôle du code source ou est disponible en tant que ressource dans votre serveur de builds. Vous devez donc la protéger en conséquence. Nous vous recommandons d’utiliser ici une clé SAP avec les autorisations *Envoyer uniquement* afin qu’un utilisateur malveillant puisse écrire dans le hub d’événements, mais sans l’écouter ni le gérer.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Configurer Azure Diagnostics pour l’envoi de journaux et de mesures vers Event Hubs
Comme indiqué précédemment, toutes les données de diagnostic par défaut et personnalisées (autrement dit, les mesures et journaux) sont automatiquement envoyées vers Stockage Azure à des intervalles configurés. Avec Event Hubs et tout récepteur supplémentaire, vous pouvez spécifier n’importe quel nœud racine ou terminal de la hiérarchie à envoyer au hub d’événements. Cela inclut les événements ETW, les compteurs de performances, les journaux des événements Windows et les journaux d’application.   

Il est important de prendre en compte le nombre de points de données qui doit réellement être transféré vers Event Hubs. En règle générale, les développeurs transfèrent des données de chemin réactif à faible latence qui doivent être consommées et interprétées rapidement. Il s’agit, par exemple, des systèmes qui analysent les alertes ou les règles de mise à l’échelle automatique. Un développeur peut également configurer un autre magasin d’analyse ou de recherche, par exemple, Azure Stream Analytics, ElasticSearch, un système de surveillance personnalisé ou un système de surveillance tiers favori.

Voici quelques exemples de configurations :

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

Dans l’exemple ci-dessus, le récepteur est appliqué au nœud parent **PerformanceCounters** dans la hiérarchie, ce qui signifie que tous les enfants **PerformanceCounters** sont envoyés à Event Hubs.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

Dans l’exemple précédent, le récepteur est appliqué uniquement à trois compteurs : **Demandes en attente**, **Demandes rejetées** et **% temps processeur**.  

L’exemple suivant montre comment un développeur peut limiter le volume de données envoyées comme mesures critiques utilisées pour l’intégrité de ce service.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

Dans cet exemple, le récepteur est appliqué aux journaux et filtré uniquement pour le suivi au niveau de l’erreur.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Déploiement et mise à jour d’une configuration de diagnostics et d’application de services cloud
Visual Studio offre le moyen le plus facile de déployer l’application et de configurer le récepteur Event Hubs. Pour afficher et modifier le fichier, ouvrez le fichier *.wadcfgx* dans Visual Studio, modifiez-le, puis enregistrez-le. Le chemin d’accès est **Projet de service cloud** > **rôles** > **(RoleName)** > **diagnostics.wadcfgx**.  

À ce stade, toutes les opérations de déploiement et de mise à jour du déploiement effectuées dans Visual Studio et dans Visual Studio Team System, de même que l’ensemble des commandes ou scripts basés sur MSBuild et qui utilisent la cible **/t:publish** incluent le fichier *.wadcfgx* dans le processus d’empaquetage. En outre, les déploiements et les mises à jour déploient le fichier vers Azure à l’aide de l’extension de l’agent Azure Diagnostics appropriée sur vos machines virtuelles.

Une fois l’application et la configuration d’Azure Diagnostics déployées, l’activité correspondante s’affiche dans le tableau de bord du hub d’événements. Cela vous indique que vous pouvez maintenant afficher les données de chemin réactif dans le client d’écouteur ou l’outil d’analyse de votre choix.  

Dans l’illustration suivante, le tableau de bord Event Hubs indique un envoi intègre de données de diagnostic vers le hub d’événements après 23h00. Cette heure correspond au moment où l’application a été déployée avec un fichier *.wadcfgx* mis à jour et où le récepteur a été correctement configuré.

![][0]  

> [!NOTE]
> Lorsque vous effectuez des mises à jour du fichier de configuration d’Azure Diagnostics (.wadcfgx), il est recommandé de les publier dans l’ensemble de l’application et de la configuration à l’aide de la publication Visual Studio ou d’un script Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Affichage des données de chemin réactif
Comme indiqué précédemment, il existe plusieurs scénarios d’utilisation pour écouter et traiter des données Event Hubs.

Une approche simple consiste à créer une petite application console de test pour écouter le hub d’événements et imprimer le flux de sortie. Vous pouvez placer le code suivant (expliqué plus en détail dans l’article [Prise en main d’Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)) dans une application console.  

Notez que l’application console doit inclure le [package Event Processor Host NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

N’oubliez pas de remplacer les valeurs entre crochets de la fonction **Main** par les valeurs de vos ressources.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>Résolution des problèmes liés aux récepteurs Event Hubs
* Le hub d’événements n’affiche pas l’activité attendue relative à des événements entrants ou sortants.

    Vérifiez que le hub d’événements a été correctement approvisionné. Toutes les informations de connexion de la section **PrivateConfig** du fichier *.wadcfgx* doivent correspondre aux valeurs de votre ressource, comme indiqué dans le portail. Assurez-vous qu’une stratégie SAS est définie (« SendRule » dans l’exemple) dans le portail et que l’autorisation *d’envoi* a été octroyée.  
* Après une mise à jour, le hub d’événements n’affiche plus d’activité relative aux événements entrants ou sortants.

    Tout d’abord, assurez-vous que les informations du hub d’événements et de la configuration sont correctes, comme nous l’avons expliqué précédemment. Le fichier **PrivateConfig** est parfois réinitialisé au cours d’une mise à jour du déploiement. La solution recommandée consiste à apporter toutes les modifications dans le projet *.wadcfgx* , puis à transmettre une mise à jour complète de l’application. Si ce n’est pas possible, assurez-vous que la mise à jour des diagnostics transmet un fichier **PrivateConfig** complet qui comprend la clé SAS.  
* J’ai essayé ces suggestions, mais le hub d’événements ne fonctionne toujours pas.

    Consultez la table Azure Storage **WADDiagnosticInfrastructureLogsTable**qui contient les journaux et les erreurs d’Azure Diagnostics. Une option consiste à utiliser un outil tel que l’ [explorateur de stockage Azure](http://www.storageexplorer.com) pour vous connecter à ce compte de stockage, consulter cette table et ajouter une requête pour l’horodatage (TimeStamp) des dernières 24 heures. Vous pouvez utiliser l’outil pour exporter un fichier .csv et l’ouvrir dans une application comme Microsoft Excel. Excel permet de rechercher facilement des chaînes de carte d’appel, telles **qu’EventHubs**afin d’identifier l’erreur signalée.  

## <a name="next-steps"></a>Étapes suivantes
•    [En savoir plus sur Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Annexe : Exemple de fichier de configuration d’Azure Diagnostics (.wadcfgx)
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Le fichier complémentaire *ServiceConfiguration.Cloud.cscfg* pour cet exemple se présente comme suit.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

Les paramètres équivalents JSON pour les machines virtuelles sont les suivants :
```JSON
"settings": {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}


"protectedSettings": {
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Créer un concentrateur d’événements](../event-hubs/event-hubs-create.md)
* [FAQ sur les hubs d'événements](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
