---
title: "Stocker et afficher des données de diagnostic dans Azure Storage | Microsoft Docs"
description: "Obtenir des données de diagnostics Microsoft Azure dans Azure Storage et les afficher"
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: adegeo
ms.openlocfilehash: 448d685cf2c0a21f4f87e672644a3cf72c2a576f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Stocker et afficher des données de diagnostic dans Azure Storage
Les données de diagnostic ne sont pas définitivement stockées, sauf si vous les transférez vers l’émulateur de stockage Microsoft Azure ou dans le stockage Azure. Une fois dans le stockage, elles peuvent être affichées avec un des outils disponibles.

## <a name="specify-a-storage-account"></a>Spécifiez un compte de stockage
Spécifiez le compte de stockage que vous souhaitez utiliser dans le fichier ServiceConfiguration.cscfg. Les informations de compte sont définies en tant que chaîne de connexion dans un paramètre de configuration. L’exemple suivant montre la chaîne de connexion par défaut créée pour un nouveau projet de service cloud dans Visual Studio :

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Vous pouvez modifier cette chaîne de connexion pour fournir des informations de compte pour un compte de stockage Azure.

Selon le type de données de diagnostic recueillies, Diagnostics Microsoft Azure utilise le service Blob ou le service de Table. Le tableau qui suit présente les sources de données permanentes et leur format.

| Source de données | Format de stockage |
| --- | --- |
| Journaux Azure |Table |
| Journaux IIS 7.0 |Blob |
| Journaux d’infrastructure de diagnostics Azure |Table |
| Journaux de suivi de requête ayant échoué |Blob |
| Journaux d’événements Windows |Table |
| Compteurs de performances |Table |
| Vidages sur incident |Blob |
| Journaux d'erreurs personnalisés |Blob |

## <a name="transfer-diagnostic-data"></a>Transférer les données de diagnostic
Pour le kit de développement logiciel 2.5 et versions ultérieures, la demande de transfert de données de diagnostic peut se produire dans le fichier de configuration. Vous pouvez transférer les données de diagnostic à des intervalles programmés, comme indiqué dans la configuration.

Pour le kit de développement logiciel 2.4 et versions antérieures, vous pouvez demander le transfert de données de diagnostic via le fichier de configuration et par programmation. L’approche par programmation vous permet également d’effectuer des transferts à la demande.

> [!IMPORTANT]
> Lorsque vous transférez des données de diagnostic vers un compte de stockage Microsoft Azure, vous encourez des frais pour les ressources de stockage qui utilise vos données de diagnostic.
> 
> 

## <a name="store-diagnostic-data"></a>Stockez les données de diagnostic
Les données du journal sont stockées dans le stockage Blob ou de Table avec les noms suivants :

**Tables**

* **WadLogsTable** : journaux rédigés dans le code à l’aide de l’écouteur de suivi.
* **WADDiagnosticInfrastructureLogsTable** : modifications de configuration et d’analyse de diagnostic.
* **WADDirectoriesTable** : répertoires que le moniteur de diagnostic surveille.  Cela inclut les journaux IIS, les journaux de requêtes ayant échoué et les répertoires personnalisés IIS.  L’emplacement du fichier journal blob est spécifié dans le champ de conteneur et le nom de l’objet blob se trouve dans le champ RelativePath.  Le champ AbsolutePath indique l’emplacement et le nom du fichier tel qu’il existait sur la machine virtuelle Azure.
* **WADPerformanceCountersTable** : les compteurs de performance.
* **WADWindowsEventLogsTable** : journaux des événements Windows.

**Objets blob**

* **wad-control-container** : (uniquement pour le kit de développement logiciel 2.4 et précédents) contient les fichiers de configuration XML qui contrôlent les diagnostics Azure.
* **wad-iis-failedreqlogfiles** : contient des informations tirées des journaux de requêtes de IIS ayant échoué.
* **wad-iis-logfiles** : contient des informations sur les journaux IIS.
* **« personnalisé »** – conteneur personnalisé basé sur la configuration des répertoires contrôlés par la surveillance de diagnostic.  Le nom de ce conteneur d’objets blobs est spécifié dans WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Outils permettant d’afficher les données de diagnostic
Plusieurs outils sont disponibles pour afficher les données après leur transfert vers le stockage. Par exemple : 

* Explorateur de serveurs dans Visual Studio : si vous avez installé Microsoft Azure Tools pour Microsoft Visual Studio, vous pouvez utiliser le nœud de stockage Azure dans l’Explorateur de serveurs pour afficher des objets blobs en lecture seule et les données du tableau depuis vos comptes de stockage Azure. Vous pouvez afficher des données à partir de votre compte d’émulateur de stockage local et de comptes de stockage que vous avez créés pour Azure. Pour plus d’informations, consultez [Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [L’explorateur de stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui vous permet d’utiliser facilement les données Azure Storage sur Windows, OSX et Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) inclut Azure Diagnostics Manager qui vous permet d’afficher, de télécharger et de gérer les données de diagnostic collectées par les applications s’exécutant dans Azure.

## <a name="next-steps"></a>Étapes suivantes
[Assurer le suivi du flux dans une application Cloud Services avec Diagnostics Azure](cloud-services-dotnet-diagnostics-trace-flow.md)

