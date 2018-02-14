---
title: "Résoudre les problèmes de services cloud à l’aide d’Application Insights | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes de service cloud en utilisant Application Insights pour traiter les données des diagnostics Azure."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Résoudre les problèmes de Cloud Services à l’aide d’Application Insights
Avec le kit [Azure SDK 2.8](https://azure.microsoft.com/downloads/) et l’extension des diagnostics Azure 1.5, vous pouvez envoyer directement à Application Insights les données Azure Diagnostics pour votre service cloud. Les journaux recueillis par Azure Diagnostics (journaux d’applications, journaux des événements Windows, journaux ETW, compteurs de performances, etc.) peuvent être envoyés à Application Insights. Vous pouvez dès lors visualiser ces informations dans l’interface utilisateur du portail Application Insights. Vous pouvez ensuite utiliser le kit SDK Application Insights pour tirer des enseignements des métriques et des journaux issus de votre application, mais aussi des données de niveau système et infrastructure issues d’Azure Diagnostics.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurer les diagnostics Azure pour envoyer des données à Application Insights
Suivez ces étapes pour configurer votre projet de service cloud de sorte qu’il envoie les données Azure Diagnostics à Application Insights.

1. Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur un rôle, puis sélectionnez **Propriétés** pour ouvrir le Concepteur de rôle.

    ![Propriétés de rôle de l'Explorateur de solutions][1]

2. Dans la section **Diagnostics** du Concepteur de rôle, sélectionnez l’option **Envoyer les données de diagnostics à Application Insights**.

    ![Le Concepteur de rôle envoie les données des diagnostics à Application Insights][2]

3. Dans la boîte de dialogue qui s’affiche, sélectionnez la ressource Application Insights à laquelle vous voulez envoyer les données Azure Diagnostics. Cette boîte de dialogue vous permet de sélectionner une ressource Application Insights existante dans votre abonnement ou de spécifier manuellement la clé d'instrumentation d’une ressource Application Insights. Pour plus d’informations sur la création d’une ressource Application Insights, consultez [Créer une ressource Application Insights](../application-insights/app-insights-create-new-resource.md).

    ![sélectionner une ressource Application Insights][3]

    Une fois que vous avez ajouté la ressource Application Insights, la clé d’instrumentation de la ressource est stockée comme paramètre de configuration de service avec le nom **APPINSIGHTS_INSTRUMENTATIONKEY**. Vous pouvez modifier ce paramètre de configuration pour chaque configuration ou environnement de service. Pour ce faire, sélectionnez une autre configuration dans la liste **Configuration du service**, puis spécifiez une nouvelle clé d’instrumentation pour cette configuration.

    ![sélectionner une configuration de service][4]

    Le paramètre de configuration **APPINSIGHTS_INSTRUMENTATIONKEY** est utilisé par Visual Studio pour configurer l’extension des diagnostics avec les informations appropriées de la ressource Application Insights lors de la publication. Le paramètre de configuration est un moyen pratique de définir une clé d'instrumentation différente pour chaque configuration de service. Visual Studio traduit ce paramètre et l'insère dans la configuration publique de l’extension des diagnostics pendant le processus de publication. Pour simplifier le processus de configuration de l’extension des diagnostics avec PowerShell, la sortie du package de Visual Studio contient aussi le fichier XML de configuration publique avec la clé d’instrumentation Application Insights appropriée. Les fichiers de configuration publique sont créés dans le dossier Extensions et suivent le modèle *PaaSDiagnostics.&lt;RoleName&gt;.PubConfig.xml*. Les déploiements basés sur PowerShell peuvent utiliser ce modèle pour mapper chaque configuration à un rôle.

4) Pour configurer les diagnostics Azure de sorte qu’ils envoient à Application Insights tous les compteurs de performances et les journaux de niveau erreur, activez l’option **Envoyer les données de diagnostic à Application Insights**. 

    Si vous voulez configurer avec plus de précision les données envoyées à Application Insights, vous devez modifier manuellement le fichier *diagnostics.wadcfgx* pour chaque rôle. Consultez [Configurer les diagnostics Azure pour envoyer des données à Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) pour en savoir plus sur la mise à jour manuelle de la configuration.

Une fois le service cloud configuré pour envoyer les données Azure Diagnostics à Application Insights, vous pouvez le déployer sur Azure normalement, en veillant à ce que l’extension des diagnostics Azure soit activée. Pour plus d’informations, consultez [Publication d’un service cloud en utilisant Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Affichage des données des diagnostics Azure dans Application Insights
Les données télémétriques de diagnostic Azure s’affichent dans la ressource Application Insights configurées pour votre service cloud.

Les types de journal Azure Diagnostics sont mappés aux concepts Application Insights comme suit :

* Les compteurs de performances s’affichent en tant que métriques personnalisées dans Application Insights.
* Les journaux des événements Windows s’affichent en tant que traces et événements personnalisés dans Application Insights.
* Les journaux d’applications, les journaux ETW et les journaux d’infrastructure de diagnostics éventuels s’affichent en tant que traces dans Application Insights.

Pour afficher les données Azure Diagnostics dans Application Insights, procédez de l’une des façons suivantes :

* Utilisez [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md) pour visualiser les compteurs de performances personnalisés ou le nombre des différents types d’événement du journal des événements Windows.

    ![Métriques personnalisées dans Metrics Explorer][5]

* Utilisez [Search](../application-insights/app-insights-diagnostic-search.md) pour effectuer des recherches dans les journaux des traces envoyés par Azure Diagnostics. Par exemple, si une exception non prise en charge a occasionné un incident et le recyclage du rôle, les informations sur l’exception s’affichent dans le canal *Application* du *Journal des événements Windows*. Vous pouvez utiliser Search pour rechercher l’erreur du Journal des événements Windows et obtenir la trace de pile complète de l’exception pour identifier la cause du problème.

    ![Rechercher dans les traces][6]

## <a name="next-steps"></a>Étapes suivantes
* [Ajouter le Kit de développement logiciel (SDK) Application Insights à votre service cloud](../application-insights/app-insights-cloudservices.md) pour envoyer des données sur les demandes, les exceptions, les dépendances et la télémétrie personnalisée à partir de votre application. En combinant ces informations avec les données Azure Diagnostics, vous pouvez obtenir une vue complète de votre application et du système dans la même ressource Application Insight.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
