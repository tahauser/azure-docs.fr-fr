---
title: Activer Application Insights Profiler pour les applications Azure Compute | Microsoft Docs
description: "Découvrez comment configurer Application Insights Profiler sur une application qui s’exécute dans Azure Compute."
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: d4559007aece8850b4c2d707686effd706ec468c
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>Activer Application Insights Profiler pour des machines virtuelles Azure, Service Fabric et Cloud Services

Cette article montre comment activer Azure Application Insights Profiler sur une application ASP.NET hébergée par une ressource de calcul Azure.

Les exemples dans cet article incluent la prise en charge de Machines virtuelles Azure, de groupes de machines virtuelles identiques, d’Azure Service Fabric et d’Azure Cloud Services. Les exemples s’appuient sur des modèles qui prennent en charge le modèle de déploiement [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Vue d'ensemble

L’image suivante montre comment Application Insights Profiler fonctionne avec les ressources Azure Compute. Les ressources Azure Compute incluent les machines virtuelles, les groupes de machines virtuelles identiques, les services cloud et les clusters Service Fabric. L’image utilise une machine virtuelle Azure comme exemple.  

  ![Vue d'ensemble](./media/enable-profiler-compute/overview.png)

Pour activer entièrement le profileur, vous devez changer la configuration dans trois emplacements :

* Panneau de l’instance Application Insights dans le portail Azure.
* Code source de l’application (par exemple, une application web ASP.NET)
* Code source de la définition du déploiement d’environnement (par exemple, un modèle Resource Manager dans un fichier .json).


## <a name="set-up-the-application-insights-instance"></a>Configurer l’instance Application Insights

[Créez une ressource Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-create-new-resource) ou sélectionnez-en une existante.
Accédez à votre ressource Application Insights et copiez la clé d’Instrumentation.

  ![Emplacement de la clé d’instrumentation](./media/enable-profiler-compute/CopyAIKey.png)

Ensuite, effectuez les étapes décrites dans [Activer le profileur](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler) pour terminer la configuration de l’instance Application Insights pour le profileur. Vous n’avez pas besoin de lier des applications web car celles-ci sont spécifiques aux étapes à la ressource App Services. Vérifiez simplement que le profileur est activé dans le panneau *Configurer* du profileur.


## <a name="set-up-the-application-source-code"></a>Configurer le code source de l’application

### <a name="aspnet-web-applications-cloud-services-web-roles-or-service-fabric-aspnet-web-frontend"></a>Applications web ASP.NET, rôles web Services cloud ou serveur frontal web ASP.NET Service Fabric
Configurez votre application pour envoyer des données de télémétrie à une instance Application Insights lors de chaque opération `Request` :  

Ajoutez le kit [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) à votre projet d’application. Vérifiez que les versions de package NuGet sont les suivantes :  
  - Pour les applications ASP.NET : [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 ou ultérieur.
  - Pour les applications ASP.NET Core : [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 ou ultérieur.
  - Pour les autres applications .NET et .NET Core (par exemple, un service sans état Service Fabric ou un rôle worker Services cloud) : [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ou [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 ou ultérieur.  

### <a name="cloud-services-worker-roles-or-service-fabric-stateless-backend"></a>Rôles worker Services cloud ou serveur principal sans état Service Fabric
Si votre application *n’est pas* une application ASP.NET ou ASP.NET Core (par exemple, s’il s’agit d’un rôle worker Services cloud ou d’API sans état Service Fabric), la configuration d’instrumentation supplémentaire suivante est nécessaire en plus de l’étape ci-dessus :  

  1. Ajoutez le code suivant au début de la durée de vie de l’application :  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  Pour plus d’informations sur cette configuration de clé d’instrumentation générale, consultez [Use Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Utiliser Service Fabric avec Application Insights).  

  2. Pour tout bloc de code que vous souhaitez instrumenter, encadrez-le d’une instruction **USING** `StartOperation<RequestTelemetry>`, comme dans l’exemple suivant :

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    ...
    var client = new TelemetryClient();
    ...
    using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
    {
      // ... Code I want to profile.
    }
    ```

  L’appel de `StartOperation<RequestTelemetry>` dans une autre étendue `StartOperation<RequestTelemetry>` n’est pas pris en charge. Vous pouvez utiliser `StartOperation<DependencyTelemetry>` dans l’étendue imbriquée à la place. Par exemple :   

    ```csharp
    using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
    {
      try
      {
        ProductDetail details = new ProductDetail() { Id = productId };
        getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

        // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
        using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
        {
            double price = await _priceDataBase.GetAsync(productId);
            if (IsTooCheap(price))
            {
                throw new PriceTooLowException(productId);
            }
            details.Price = price;
        }

        // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
        using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
        {
            details.Reviews = await _reviewDataBase.GetAsync(productId);
        }

        getDetailsOperation.Telemetry.Success = true;
        return details;
      }
      catch(Exception ex)
      {
        getDetailsOperation.Telemetry.Success = false;

        // This exception gets linked to the 'GetProductDetails' request telemetry.
        client.TrackException(ex);
        throw;
      }
    }
    ```


## <a name="set-up-the-environment-deployment-definition"></a>Configurer la définition du déploiement d’environnement

L’environnement dans lequel s’exécutent le profileur et votre application peut être une machine virtuelle, un groupe de machines virtuelles identiques, un cluster Service Fabric ou une instance Services cloud.  

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>Machines virtuelles, groupe de machines virtuelles identiques ou Service Fabric

Exemples complets :  
  * [Machine virtuelle](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Groupe de machines virtuelles identiques](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Cluster Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Pour vérifier que le [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou ultérieur est utilisé, il suffit de confirmer que le système d’exploitation déployé est `Windows Server 2012 R2` ou ultérieur.

2. Recherchez l’extension [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) dans le fichier de modèle de déploiement, puis ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg`. Remplacez la valeur de propriété `ApplicationInsightsProfiler` par votre propre clé d’instrumentation Application Insights :  
  ```json
  "SinksConfig": {
    "Sink": [
      {
        "name": "MyApplicationInsightsProfilerSink",
        "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
      }
    ]
  }
  ```

  Pour plus d’informations sur l’ajout de l’extension Diagnostics à votre modèle de déploiement, consultez [Utiliser la surveillance et les diagnostics avec une machine virtuelle Windows et des modèles Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


### <a name="cloud-services"></a>Cloud Services

1. Pour vérifier que le [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou ultérieur est utilisé, il suffit de confirmer que les fichiers ServiceConfiguration.\*.cscfg ont une valeur `osFamily` au moins égale à **« 5 »**.

2. Recherchez le fichier diagnostics.wadcfgx [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) pour votre rôle d’application :  
  ![Emplacement du fichier de configuration de diagnostics](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  Pour savoir comment activer l’extension Diagnostics dans votre projet Services cloud si vous ne trouvez pas le fichier, consultez [Configurer les diagnostics pour les services cloud et les machines virtuelles Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Ajoutez la section `SinksConfig` suivante comme élément enfant de `WadCfg` :  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> Si le fichier `diagnostics.wadcfgx` contient également un autre récepteur de type `ApplicationInsights`, ces trois clés d’instrumentation doivent correspondre :  
>  * Clé d’instrumentation utilisée par votre application  
>  * Clé d’instrumentation utilisée par le récepteur `ApplicationInsights`  
>  * Clé d’instrumentation utilisée par le récepteur `ApplicationInsightsProfiler`  
>
> Vous trouverez la valeur réelle de la clé d’instrumentation utilisée par le récepteur `ApplicationInsights` dans les fichiers ServiceConfiguration.\*.cscfg.  
> Après la publication du kit SDK Azure Visual Studio 15.5, seules les clés d’instrumentation utilisées par l’application et le récepteur `ApplicationInsightsProfiler` doivent correspondre.


## <a name="environment-deployment-and-runtime-configurations"></a>Configurations de runtime et déploiement d’environnement

1. Déployez la définition du déploiement d’environnement modifiée.  

  Pour appliquer les modifications, vous devez généralement effectuer un déploiement de modèle complet ou une publication des services cloud avec les applets de commande PowerShell ou Visual Studio.  

  Voici une autre approche pour des machines virtuelles existantes qui touche uniquement son extension Azure Diagnostics :  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. Si l’application souhaitée s’exécute avec [IIS](https://www.microsoft.com/web/platform/server.aspx), activez la fonctionnalité Windows `IIS Http Tracing` :  

  1. Établissez l’accès à distance à l’environnement, puis utilisez la fenêtre [Ajouter des fonctionnalités Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ou exécutez la commande suivante dans PowerShell (en tant qu’administrateur) :  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. Si l’établissement de l’accès à distance pose problème, vous pouvez utiliser [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) pour exécuter la commande suivante :  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>Activer le profileur sur des serveurs locaux

L’activation du profileur sur un serveur local correspond à l’exécution d’Application Insights Profiler en mode autonome (il n’est pas lié aux modifications de l’extension Diagnostics Azure).

Nous ne prévoyons pas une prise en charge officielle du profileur pour les serveurs locaux. Si vous souhaitez tester ce scénario, vous pouvez [télécharger le code de prise en charge](https://github.com/ramach-msft/AIProfiler-Standalone). Nous *ne sommes pas* responsables de la mise à jour de ce code, et nous ne répondons pas aux demandes de fonctionnalités ni aux questions relatives à des problèmes liés au code.

## <a name="next-steps"></a>Étapes suivantes

- Générez le trafic vers votre application (par exemple, lancez un [test de disponibilité](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Attendez ensuite 10 à 15 minutes le temps que les traces soient envoyées à l’instance Application Insights.
- Consultez [Traces du profileur](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) dans le portail Azure.
- Consultez l’article [Résolution des problèmes du profileur](app-insights-profiler.md#troubleshooting) pour obtenir de l’aide pour résoudre les problèmes rencontrés avec le profileur.
- Pour en savoir plus sur le profileur, consultez l’article [Application Insights Profiler](app-insights-profiler.md).
