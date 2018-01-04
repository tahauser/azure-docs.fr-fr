---
title: "Exceptions FabricClient fréquemment levées | Microsoft Docs"
description: "Décrit les exceptions et les erreurs courantes qui peuvent être générées par les API FabricClient lors des opérations de gestion des applications et des clusters."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/18/2017
ms.author: ryanwi
ms.openlocfilehash: f07b1bce2e9ccc82e3dcbfe0f2cfe2311d968505
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2017
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Exceptions et erreurs courantes lorsque vous travaillez avec les API FabricClient
Les API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) permettent aux administrateurs de clusters et d’applications d’effectuer des tâches administratives au niveau d’une application, d’un service ou d’un cluster Service Fabric. Par exemple : déploiement d’une application, mise à niveau, suppression, vérification de l'intégrité d’un cluster, ou test d’un service. Les développeurs d'applications et les administrateurs de clusters peuvent utiliser les API FabricClient pour développer des outils permettant de gérer le cluster et les applications Service Fabric.

Il existe de nombreux types d'opérations qui peuvent être effectuées à l'aide de FabricClient.  Chaque méthode peut générer des exceptions pour les erreurs dues à une saisie incorrecte, des erreurs d'exécution ou des problèmes d'infrastructure temporaires.  Consultez la documentation de référence sur les API pour identifier les exceptions générées par une méthode spécifique. Quelques exceptions peuvent cependant être levées par de nombreuses autres API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient). Le tableau suivant répertorie les exceptions communes aux API FabricClient.

| Exception | Générée lorsque |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |L’objet [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) est à l’état fermé. Supprimez l’objet [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) que vous utilisez et instanciez un nouvel objet [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient). |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |L'opération a expiré. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) est renvoyé lorsque la durée de l’opération dépasse la valeur MaxOperationTimeout. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |La vérification d'accès pour l'opération a échoué. E_ACCESSDENIED est renvoyée. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Une erreur d'exécution s'est produite lors de l'opération. N’importe quelle méthode FabricClient peut potentiellement générer une exception [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException). La propriété [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) indique la cause exacte de l’exception. Les codes d’erreur sont définis dans l’énumération [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode). |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |L'opération a échoué en raison d'une condition d'erreur transitoire quelconque. Par exemple, une opération peut échouer si un quorum de réplicas est temporairement inaccessible. Les exceptions temporaires correspondent à des opérations ayant échoué et qui peuvent être relancées. |

Certaines erreurs [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) courantes peuvent être retournées dans une exception [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) :

| Error | Condition |
| --- |:--- |
| CommunicationError |Une erreur de communication a provoqué l’échec de l'opération, retentez l'opération. |
| InvalidCredentialType |Le type d'informations d'identification n'est pas valide. |
| InvalidX509FindType |La valeur X509FindType n'est pas valide. |
| InvalidX509StoreLocation |L’emplacement du magasin X509 n'est pas valide. |
| InvalidX509StoreName |Le nom du magasin X509 n'est pas valide. |
| InvalidX509Thumbprint |La chaîne de l’empreinte de certificat X509 n'est pas valide. |
| InvalidProtectionLevel |Le niveau de protection n'est pas valide. |
| InvalidX509Store |Le magasin de certificats X509 ne peut pas être ouvert. |
| InvalidSubjectName |Le nom d'objet n'est pas valide. |
| InvalidAllowedCommonNameList |Le format de la chaîne de la liste de noms courants n'est pas valide. Ce doit être une liste séparée par des virgules. |

