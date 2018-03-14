---
title: "Mettre à niveau un runtime Azure Service Fabric | Microsoft Docs"
description: "Dans ce didacticiel, vous découvrez comment utiliser PowerShell pour mettre à niveau le runtime d’un cluster Service Fabric hébergé par Azure."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 49211a88e004bbcbcc41b6674a34934db39513c7
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster"></a>Didacticiel : Mettre à niveau le runtime d’un cluster Service Fabric

Ce didacticiel est la troisième partie d’une série de didacticiels et vous montre comment mettre à niveau le runtime Service Fabric sur un cluster Azure Service Fabric. Cette partie du didacticiel concerne les clusters Service Fabric s’exécutant sur Azure ; elle ne s’applique pas aux clusters Service Fabric autonomes.

> [!WARNING]
> Cette partie du didacticiel nécessite PowerShell. Les outils Azure CLI n’assurent pas la prise en charge de la mise à niveau du runtime d’un cluster. Cependant, vous pouvez mettre à niveau un cluster dans le portail. Pour plus d’informations, consultez [Mettre à niveau un cluster Azure Service Fabric](service-fabric-cluster-upgrade.md).

Si votre cluster exécute déjà le dernier runtime Service Fabric, vous n’avez pas besoin d’effectuer cette étape. Toutefois, vous pouvez utiliser cet article pour installer n’importe quel runtime pris en charge sur un cluster Azure Service Fabric.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Obtenir la version du cluster
> * Définir la version du cluster

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * créer un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure à l’aide d’un modèle ;
> * [Mettre à l’échelle un cluster](service-fabric-tutorial-scale-cluster.md)
> * Mettre à niveau le runtime d’un cluster
> * [déployer la Gestion des API avec Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez le [module Azure PowerShell version 4.1 ou ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Créer un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure
- Si vous déployez un cluster Windows, configurez un environnement de développement Windows. Installez les charges de travail [Visual Studio 2017](http://www.visualstudio.com) et le **développement Azure**, **ASP.NET et le développement web**, ainsi que **Développement multiplateforme .NET Core**.  Ensuite, configurez un [environnement de développement .NET](service-fabric-get-started.md).
- Si vous déployez un cluster Linux, configurez un environnement de développement Java sur [Linux](service-fabric-get-started-linux.md) ou [MacOS](service-fabric-get-started-mac.md).  Installez l’[interface de ligne de commande (CLI) de Service Fabric](service-fabric-cli.md). 

### <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous à votre compte Azure, sélectionnez votre abonnement avant d’exécuter des commandes Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Obtenir la version du runtime

Après vous être connecté à Azure et avoir sélectionné l’abonnement contenant le cluster Service Fabric, vous pouvez obtenir la version du runtime du cluster.

```powershell
Get-AzureRmServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

Ou, obtenez simplement la liste de tous les clusters dans votre abonnement en exécutant la commande suivante :

```powershell
Get-AzureRmServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Notez la valeur **ClusterCodeVersion**. Vous l’utiliserez dans la section suivante.

## <a name="upgrade-the-runtime"></a>Mettre à niveau le runtime

Avec l’applet de commande `Get-ServiceFabricRuntimeUpgradeVersion`, utilisez la valeur **ClusterCodeVersion** obtenue dans la section précédente pour découvrir les versions vers lesquelles vous pouvez effectuer la mise à niveau. Vous pouvez exécuter cette applet de commande uniquement à partir d’un ordinateur connecté à Internet. Par exemple, pour connaître les versions de runtime vers lesquelles vous pouvez effectuer une mise à niveau depuis la version `5.7.198.9494`, utilisez la commande suivante :

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Une liste de versions vous permet de mettre à niveau le cluster Azure Service Fabric vers un runtime plus récent. Par exemple, si une mise à niveau vers la version `6.0.219.9494` est possible, utilisez la commande suivante pour mettre à niveau votre cluster.

```powershell
Set-AzureRmServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> La mise à niveau du runtime du cluster peut prendre beaucoup de temps. PowerShell est bloqué pendant l’exécution de la mise à niveau. Vous pouvez utiliser une autre session PowerShell pour vérifier l’état de la mise à niveau.

Vous pouvez surveiller l’état de la mise à niveau avec PowerShell ou l’interface CLI `sfctl`.

Tout d’abord, connectez-vous au cluster avec le certificat SSL créé dans la première partie du didacticiel. Utilisez l’applet de commande `Connect-ServiceFabricCluster` ou `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Ensuite, utilisez `Get-ServiceFabricClusterUpgrade` ou `sfctl cluster upgrade-status` pour afficher l’état. Le résultat affiché est semblable à ce qui suit.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="conclusion"></a>Conclusion
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Obtenir la version du runtime du cluster
> * Mettre à niveau le runtime du cluster
> * Surveiller la mise à niveau

Ensuite, passez au didacticiel suivant pour apprendre à déployer la Gestion des API avec un cluster Service Fabric.
> [!div class="nextstepaction"]
> [déployer la Gestion des API avec Service Fabric](service-fabric-tutorial-deploy-api-management.md).
