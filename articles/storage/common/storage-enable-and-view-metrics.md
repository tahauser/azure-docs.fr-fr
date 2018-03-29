---
title: Activation des métriques de stockage dans le portail Azure | Microsoft Docs
description: Activation des métriques de stockage pour les services d’objet Blob, de File d’attente, de Table et de Fichier
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.openlocfilehash: 0caa4eff80877ad4bf8d501a276e82922b1a84c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Activation des métriques Azure Storage et affichage des données associées
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Vue d'ensemble
Storage Metrics est activé par défaut lorsque vous créez un compte de stockage. Vous pouvez configurer la surveillance par le biais du [Portail Azure](https://portal.azure.com) ou de Windows PowerShell, ou par programme au moyen d’une des bibliothèques clientes de stockage.

Vous pouvez choisir une période de rétention des données : cette période détermine combien de temps le service de stockage conserve les métriques et la durée pendant laquelle l’espace requis pour les stocker vous est facturé. En règle générale, il est recommandé d’utiliser une période de rétention plus courte pour les métriques par minute que pour les métriques par heure, en raison de l’espace supplémentaire requis. La période de rétention que vous définissez doit être suffisamment longue pour vous donner le temps d’analyser les données et de télécharger les métriques à conserver à des fins d’analyse ou de création de rapports hors connexion. N’oubliez pas que le téléchargement des données de métriques depuis votre compte de stockage est aussi facturé.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Activer les mesures à l’aide du Portail Azure
Pour activer les mesures dans le [Portail Azure](https://portal.azure.com), suivez les étapes suivantes :

1. Accédez à votre compte de stockage.
1. Sélectionnez **Diagnostics** sur le panneau **Menu**.
1. Vérifiez que l’option **État** est définie sur **Activé**.
1. Sélectionnez les métriques des services que vous souhaitez surveiller.
1. Spécifiez une stratégie de rétention pour indiquer la durée de conservation des métriques et de journalisation des données.
1. Sélectionnez **Enregistrer**.

Le [Portail Azure](https://portal.azure.com) ne vous permet pas de configurer des métriques par minute dans votre compte de stockage ; vous devez les activer avec PowerShell ou par programmation.

## <a name="how-to-enable-metrics-using-powershell"></a>Comment activer les métriques à l’aide de PowerShell
Vous pouvez utiliser PowerShell sur votre ordinateur local pour configurer Storage Metrics dans votre compte de stockage. Utilisez l’applet de commande Azure PowerShell Get-AzureStorageServiceMetricsProperty pour récupérer les paramètres actuels et l’applet de commande Set-AzureStorageServiceMetricsProperty pour modifier les paramètres actuels.

Les applets de commande qui contrôlent Storage Metrics utilisent les paramètres suivants :

* MetricsType peut avoir pour valeur Hour ou Minute.
* ServiceType peut avoir pour valeur Blob, Queue ou Table.
* MetricsLevel peut avoir pour valeur None, Service et ServiceAndApi.

Par exemple, la commande suivante active les métriques par minute pour le service BLOB dans votre compte de stockage par défaut avec une période de rétention de cinq jours :

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

La commande suivante récupère le niveau actuel des métriques par heure et la période de rétention en jours pour le service BLOB dans votre compte de stockage par défaut :

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Pour plus d’informations sur la configuration des applets de commande Azure PowerShell avec votre abonnement Azure et sur la sélection du compte de stockage par défaut à utiliser, voir [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Comment activer Storage Metrics par programmation
L’extrait de code C# suivant montre comment activer les métriques et la journalisation pour le service BLOB à l’aide de la bibliothèque cliente de stockage pour .NET :

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Affichage des métriques de stockage
Après que vous avez configuré les métriques d’analyse du stockage pour surveiller votre compte de stockage, l’analyse du stockage enregistre les métriques dans des tables connues dans votre compte de stockage. Vous pouvez configurer des graphiques permettant de consulter des mesures horaires dans le [Portail Azure](https://portal.azure.com) :

1. Accédez à votre compte de stockage dans le [Portail Azure](https://portal.azure.com).
1. Sélectionnez **Mesures** dans le volet **Menu** du service dont vous souhaitez afficher les mesures.
1. Sélectionnez **Modifier** sur le graphique que vous souhaitez configurer.
1. Dans le volet **Modifier le graphique**, sélectionnez l’**Intervalle de temps**, le **Type de graphique** et les mesures que vous souhaitez afficher dans le graphique.
1. Sélectionnez **OK**.

Si vous souhaitez télécharger les métriques pour un stockage à long terme ou pour les analyser localement, vous devez :

* Utiliser un outil qui prend en charge ces tables et vous permet de les afficher et de les télécharger
* Écrire une application ou un script personnalisé pour lire et stocker les tables

De nombreux outils de consultation du stockage tiers prennent en charge ces tables et vous permettent de les afficher directement.
Pour obtenir la liste des outils disponibles, consultez [Outils clients Azure Storage](storage-explorers.md) .

> [!NOTE]
> À compter de la version 0.8.0 de [Microsoft Azure Storage Explorer](http://storageexplorer.com/), vous pourrez désormais afficher et télécharger les tables de métriques Analytics.
> 
> 

Pour accéder par programmation aux tables Analytics, notez qu’elles n’apparaissent pas si vous répertoriez toutes les tables dans votre compte de stockage. Vous pouvez y accéder directement par nom ou utiliser l [’API CloudAnalyticsClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) dans la bibliothèque cliente .NET pour interroger les noms de tables.

### <a name="hourly-metrics"></a>Métriques toutes les heures
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Métriques par minute
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacity
* $MetricsCapacityBlob

Vous trouverez des informations complètes sur les schémas de ces tables dans [Schéma de table de métriques Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx). Les exemples de lignes ci-dessous montrent uniquement un sous-ensemble des colonnes disponibles, mais ils illustrent les différentes façons dont Storage Metrics enregistre ces métriques :

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilité | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |user;QueryEntities |2014-05-22T11:01:16.7640250Z |5. |5. |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9. |6. |100 |

Dans cet exemple de données de métriques par minute, la clé de partition (PartitionKey) utilise une résolution d’une minute. La clé de ligne identifie le type d’informations stockées dans la ligne. La clé de ligne est composée de deux éléments d’informations, le type d’accès et le type de demande :

* Le type d’accès a la valeur user ou system, user correspondant à toutes les demandes de l’utilisateur au service de stockage et system correspondant à toutes les demandes formulées par Storage Analytics.
* Le type de demande peut avoir la valeur all, auquel cas il s’agit d’une ligne de résumé, ou il identifie l’API spécifique comme QueryEntity ou UpdateEntity.

Les exemples de données ci-dessus montrent tous les enregistrements pour une seule minute (à partir de 11h00). Ainsi, la somme des demandes QueryEntities, QueryEntity et UpdateEntity est égale à sept, ce qui correspond bien au total indiqué sur la ligne user:All. De même, vous pouvez déduire la latence de bout en bout moyenne (104,4286) sur la ligne user:All en effectuant le calcul suivant : ((143,8 * 5) + 3 + 9)/7.

## <a name="metrics-alerts"></a>Alertes liées aux métriques
Il peut être intéressant de définir des alertes dans le [Portail Azure](https://portal.azure.com), afin que Storage Metrics puisse vous avertir automatiquement de tout changement important de comportement de vos services de stockage. Si vous utilisez un outil Explorateur de stockage pour télécharger ces données dans un format délimité, vous pouvez utiliser Microsoft Excel pour les analyser. Pour obtenir la liste des outils d’exploration du stockage disponibles, consultez [Outils clients Azure Storage](storage-explorers.md) . Vous pouvez configurer des alertes dans le volet **Règles d’alerte**, accessible sous **Surveillance** dans le volet du menu Compte de stockage.

> [!IMPORTANT]
> Il peut y avoir un décalage entre un événement de stockage et l’enregistrement des données métriques par heure ou par minute correspondantes. Lorsque vous enregistrez des métriques par minute, plusieurs minutes de données peuvent être écrites à la fois. Des transactions de minutes précédentes pourraient alors être agrégées dans la transaction pour la minute actuelle. Dans ce cas, le service d’alerte peut ne pas disposer de toutes les données de métriques disponibles pour l’intervalle d’alerte configuré, ce qui peut conduire à des déclenchements d’alerte inattendus.
>

## <a name="accessing-metrics-data-programmatically"></a>Accès aux données de métriques par programmation
L’exemple de code suivant en C# accède aux métriques par minute pour une plage de minutes et affiche les résultats dans une fenêtre de console. Il utilise la version 4 de la bibliothèque de stockage Azure. Celle-ci comprend la classe CloudAnalyticsClient, qui simplifie l’accès aux tables de métriques de stockage.

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
        Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
        var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
        var t = analyticsClient.GetMinuteMetricsTable(service);
        var opContext = new OperationContext();
        var query =
          from entity in metricsQuery
          // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
          // because they are calculated fields in the MetricsEntity class.
          // The PartitionKey identifies the DataTime of the metrics.
          where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
        select entity;

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
        var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
        var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
        Console.WriteLine(resultString);
    }
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)
{
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
      string.Format("Time: {0}, ", entity.Time) +
      string.Format("AccessType: {0}, ", entity.AccessType) +
      string.Format("TransactionType: {0}, ", entity.TransactionType) +
      string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
}
```

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Quels sont les frais encourus quand vous activez les métriques de stockage ?
Les demandes d’écriture pour créer des entités de table pour les métriques sont facturées au tarif standard applicable à toutes les opérations Azure Storage.

Les demandes de lecture et de suppression formulées par un client sur des données de métriques sont aussi facturées au tarif standard. Si vous avez configuré une stratégie de rétention des données, vous n’êtes pas facturé quand Azure Storage supprime les anciennes données de métriques. Toutefois, si vous supprimez des données de métriques, les opérations de suppression sont facturées à votre compte.

La capacité utilisée par les tables de métriques est également facturée ; vous pouvez utiliser les informations suivantes pour estimer la capacité utilisée pour stocker les données de métriques :

* En une heure, pour un service qui utilise toutes les API de chaque service, environ 148 Ko de données sont stockés par heure dans les tables de transaction de métriques si vous avez activé la synthèse au niveau des services et des API.
* En une heure, pour un service qui utilise toutes les API de chaque service, environ 12 Ko de données sont stockés par heure dans les tables de transaction de métriques si vous avez uniquement activé la synthèse au niveau des services.
* Deux lignes supplémentaires sont ajoutées chaque jour à la table de capacité pour les objets blob (si l’utilisateur a activé les journaux). Cela signifie que la table augmente d’environ 300 octets au maximum par jour.

## <a name="next-steps"></a>Étapes suivantes
[Activation de la journalisation du stockage et accès aux données des journaux](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
