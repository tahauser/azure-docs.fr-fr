---
title: "Mettre à niveau un cluster Azure Service Fabric autonome sur Windows Server | Microsoft Docs"
description: "Mettez à niveau le code et/ou la configuration Azure Service Fabric qui exécute un cluster Service Fabric autonome, notamment en définissant le mode de mise à jour du cluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: c95c1827d0433dcb61eace34e7a905a5610c7781
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Mettre à niveau un cluster Azure Service Fabric autonome sur Windows Server 
> [!div class="op_single_selector"]
> * [Cluster Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)
>
>

Pour les systèmes modernes, la possibilité de mettre à niveau est essentielle à la réussite à long terme de votre produit. Un cluster Azure Service Fabric est une ressource que vous possédez. Cet article explique comment garantir que le cluster exécute toujours des versions prises en charge du code et des configurations Service Fabric.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Contrôler la version de Service Fabric qui s’exécute sur votre cluster
Pour configurer votre cluster afin qu’il télécharge les mises à jour de Service Fabric quand Microsoft publie une nouvelle version, affectez la valeur *true* à la configuration de cluster fabricClusterAutoupgradeEnabled. Pour sélectionner une version prise en charge de Service Fabric pour votre cluster, affectez la valeur *false* à la configuration de cluser fabricClusterAutoupgradeEnabled.

> [!NOTE]
> Veillez à ce que votre cluster exécute toujours une version de Service Fabric prise en charge. Lorsque Microsoft annonce le lancement d’une nouvelle version de Service Fabric, la fin de la prise en charge de la version précédente est signalée 60 jours minimum après la date de l’annonce. Les nouvelles versions sont annoncées [sur le blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). La nouvelle version peut alors être sélectionnée.
>
>

Vous ne pouvez mettre à niveau votre cluster vers la nouvelle version que si vous utilisez une configuration de nœud de type production, où chaque nœud Service Fabric est alloué sur un ordinateur physique ou une machine virtuelle distincts. Si vous disposez d’un cluster de développement dans lequel plusieurs nœuds Service Fabric se trouvent sur un seul ordinateur physique ou une seule machine virtuelle, vous devez recréer le cluster avec la nouvelle version.

Deux workflows distincts permettent de mettre à niveau votre cluster vers la dernière version ou une version prise en charge de Service Fabric. L’un concerne les clusters disposant d’une connectivité suffisante pour télécharger automatiquement la version la plus récente. L’autre workflow est destiné aux clusters ne disposant pas d’une connectivité suffisante pour télécharger la version de Service Fabric la plus récente.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les clusters disposant d’une connectivité suffisante pour télécharger le code et la configuration les plus récents
Si vos nœuds de cluster disposent d’une connectivité Internet au [Centre de téléchargement Microsoft](http://download.microsoft.com), suivez les étapes ci-après pour mettre à niveau votre cluster vers une version prise en charge.

Pour les clusters qui disposent d’une connectivité au [Centre de téléchargement Microsoft](http://download.microsoft.com), Microsoft vérifie régulièrement la disponibilité de nouvelles versions de Service Fabric.

Quand une nouvelle version de Service Fabric est disponible, le package est téléchargé localement dans le cluster et configuré pour la mise à niveau. De plus, pour informer l’utilisateur de cette nouvelle version, le système montre un avertissement explicite concernant l’intégrité du cluster, semblable à ce qui suit :

« La prise en charge de la version actuelle du cluster [numéro de version] se terminera le [date]. »

Une fois que le cluster exécute la version la plus récente, l’avertissement est retiré.

#### <a name="cluster-upgrade-workflow"></a>Flux de travail de mise à niveau de cluster
Quand l’avertissement concernant l’intégrité du cluster s’affiche, effectuez les étapes suivantes :

1. Connectez-vous au cluster à partir d’une machine virtuelle disposant d’un accès administrateur à toutes les machines qui sont répertoriées en tant que nœuds dans le cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster.

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obtenez la liste des versions de Service Fabric vers lesquelles vous pouvez effectuer une mise à niveau.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    La sortie est normalement de ce type :

    ![Obtenir les versions de Service Fabric prises en charge][getfabversions]
3. Démarrez une mise à niveau du cluster vers une version disponible avec la commande Windows PowerShell [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Pour surveiller la progression de la mise à niveau, vous pouvez utiliser Service Fabric Explorer ou exécuter la commande PowerShell suivante :

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Si les stratégies d’intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Pour spécifier des stratégies d’intégrité personnalisées pour la commande ServiceFabricClusterUpgrade, consultez la documentation de [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Après avoir corrigé les problèmes ayant entraîné la restauration, relancez la mise à niveau, en suivant les étapes décrites précédemment.

### <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les clusters *ne disposant pas d’une connectivité* suffisante pour télécharger le code et la configuration les plus récents
Si vos nœuds de cluster disposent d’une connectivité Internet au [Centre de téléchargement Microsoft](http://download.microsoft.com), suivez les étapes ci-après pour mettre à niveau votre cluster vers une version prise en charge.

> [!NOTE]
> Si vous utilisez un cluster qui n’est pas connecté à Internet, vous devrez consulter régulièrement le blog de l’équipe Service Fabric pour être averti de la disponibilité des nouvelles versions. Le système n’affiche pas d’avertissement concernant l’intégrité du cluster vous alertant en cas de disponibilité d’une nouvelle version.  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>Provisionnement automatique vs. provisionnement manuel
Pour activer le téléchargement et l’inscription automatiques pour la dernière version du code, configurez le service de mise à jour de Service Fabric. Pour obtenir des instructions, consultez Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt dans le [package autonome](service-fabric-cluster-standalone-package-contents.md).
Pour le processus manuel, procédez comme suit.

Modifiez la configuration du cluster pour affecter la valeur *false* à la propriété suivante avant de commencer la mise à niveau d’une configuration :

        "fabricClusterAutoupgradeEnabled": false,

Pour plus de détails, consultez la [commande PowerShell Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Veillez à mettre à jour « clusterConfigurationVersion » dans votre code JSON avant de commencer la mise à niveau de la configuration.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Flux de travail de mise à niveau de cluster

1. Exécutez Get-ServiceFabricClusterUpgrade à partir de l’un des nœuds du cluster et notez la valeur de TargetCodeVersion.

2. Exécutez la commande suivante à partir d’un ordinateur connecté à Internet pour répertorier toutes les versions de mise à niveau compatibles avec la version actuelle et téléchargez le package correspondant à l’aide des liens de téléchargement associés :

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Connectez-vous au cluster à partir d’une machine virtuelle disposant d’un accès administrateur à toutes les machines qui sont répertoriées en tant que nœuds dans le cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Copiez le package téléchargé dans le magasin d’images du cluster.

5. Inscrivez le package copié.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Commencez une mise à niveau de cluster vers une version disponible.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Vous pouvez surveiller la progression de la mise à niveau avec Service Fabric Explorer ou exécuter la commande PowerShell suivante :

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Si les stratégies d’intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Pour spécifier des stratégies d’intégrité personnalisées pour la commande ServiceFabricClusterUpgrade, consultez la documentation de [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Après avoir corrigé les problèmes ayant entraîné la restauration, relancez la mise à niveau, en suivant les étapes décrites précédemment.


## <a name="upgrade-the-cluster-configuration"></a>Mettre à niveau la configuration du cluster
Avant de lancer la mise à niveau de la configuration, vous pouvez tester votre nouveau modèle JSON de configuration de cluster en exécutant le script PowerShell suivant dans le package autonome :

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
Ou utilisez ce script :

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

Certaines configurations ne peuvent pas être mises à niveau, notamment les points de terminaison, le nom du cluster, l’IP du nœud, etc. Cette opération teste le nouveau modèle JSON de configuration de cluster en le comparant à l’ancien modèle, et consigne les erreurs dans la fenêtre PowerShell en cas de problème.

Pour mettre à niveau la configuration du cluster, exécutez Start-ServiceFabricClusterConfigurationUpgrade. La mise à niveau de la configuration est traitée par domaine de mise à niveau.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Mise à niveau de la configuration du certificat de cluster  
Un certificat de cluster est utilisé pour l’authentification entre les nœuds de cluster. La substitution de certificat doit être effectuée avec prudence, car toute défaillance bloque la communication entre les nœuds de cluster.

Techniquement, quatre options sont prises en charge :  

* Mise à niveau de certificat unique : le chemin de la mise à niveau est « Certificat A (principal) -> Certificat B (principal) -> Certificat C (principal) ->....

* Double mise à niveau de certificat : le chemin de la mise à niveau est « Certificat A -> (principal) -> Certificat A (principal) et B (secondaire) -> Certificat B (principal) -> Certificat B (principal) et C (secondaire) -> Certificat C (principal) ->....

* Mise à niveau du type de certificat : configuration de certificats basée sur Thumbprint <> configuration de certificats basée sur CommonName. Par exemple, certificat Thumbprint A (Principal) et Thumbprint B (Secondaire) -> certificat CommonName C.

* Mise à niveau de l’empreinte numérique de l’émetteur de certificats : le chemin de mise à niveau est Certificate CN=A,IssuerThumbprint=IT1 (Primary) -> Certificate CN=A,IssuerThumbprint=IT1,IT2 (Primary) -> Certificate CN=A,IssuerThumbprint=IT2 (Primary).


## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment personnaliser certains [paramètres de clusters Service Fabric](service-fabric-cluster-fabric-settings.md).
* Découvrez comment [diminuer et augmenter la taille de votre cluster](service-fabric-cluster-scale-up-down.md).
* Découvrez les [mises à niveau d’applications](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
