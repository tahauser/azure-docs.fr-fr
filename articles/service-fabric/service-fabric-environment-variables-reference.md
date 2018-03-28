---
title: Variables d’environnement Azure Service Fabric | Microsoft Docs
description: Documentation de référence pour les variables d’environnement Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: a9faefb43b9d5da81dddef8f326a3867b32842f7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-environment-variables"></a>Variables d’environnement de Service Fabric

Des variables d’environnement définies pour chaque instance de service sont intégrées à Service Fabric. Voici la liste complète des variables d’environnement :

| Variable d’environnement                         | Description                                                            | Exemples                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Nom de l’URI Fabric de l'application                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | Nom du package de code auquel appartient le processus              | Code                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Adresse IP ou nom de domaine complet du point de terminaison                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Numéro de port du point de terminaison                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Dossier du journal                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Dossier temporaire                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Dossier de travail                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Dossier de base des applications                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Valeur booléenne qui spécifie si le processus est un conteneur                   | false                                                                |
| Fabric_NodeId                                | ID du nœud qui exécute le processus                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Adresse IP ou nom de domaine complet du nœud, tel que spécifié dans le fichier manifeste du cluster. | localhost ou 10.0.0.1                                                |
| Fabric_NodeName                              | Nom du nœud qui exécute le processus                          | _Node_0                                                              |
| Fabric_ServiceName                           | Le nom du service, si le service est hébergé en mode ExclusiveProcess. Cette valeur de variable est uniquement disponible si vous créez le service avec ServicePackageActivationMode ExclusiveProcess.  | MyService                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | Identificateur global unique                                                               |
| Fabric_ServicePackageName                    | Nom du package de service dont fait partie le processus                     | Web1Pkg                                                              |

Variables d’environnement internes utilisées par le runtime Service Fabric :

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName