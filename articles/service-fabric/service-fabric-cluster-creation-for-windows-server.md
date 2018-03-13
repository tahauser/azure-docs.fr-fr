---
title: "Créer un cluster Azure Service Fabric autonome | Microsoft Docs"
description: "Créez un cluster Azure Service Fabric sur n’importe quel ordinateur (physique ou virtuel) exécutant Windows Server, qu’il soit local ou dans un cloud."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: dekapur;chackdan;maburlik
ms.openlocfilehash: 7d65845d21205e22a3e5b0868acb637ff614fb42
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Créer un cluster autonome s’exécutant sur Windows Server
Vous pouvez utiliser Azure Service Fabric pour créer des clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server. Cela signifie que vous pouvez déployer et exécuter des applications Service Fabric dans n’importe quel environnement contenant un ensemble d’ordinateurs Windows Server interconnectés, que ce soit en local ou avec un fournisseur cloud. Service Fabric fournit un package d’installation pour créer des clusters Service Fabric, appelé package Windows Server autonome.

Cet article vous guide tout au long du processus de création d’un cluster Service Fabric autonome.

> [!NOTE]
> Ce package de Windows Server autonome est commercialisé et peut être utilisé pour les déploiements de production. Ce package peut contenir de nouvelles fonctionnalités Service Fabric en version « préliminaire ». Faites défiler jusqu’à la section « [Fonctionnalités préliminaires incluses dans ce package](#previewfeatures_anchor). » pour obtenir la liste des fonctionnalités préliminaires. Vous pouvez [télécharger une copie du CLUF](http://go.microsoft.com/fwlink/?LinkID=733084) maintenant.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Obtenir de l’aide pour le package Service Fabric pour Windows Server
* Interrogez la Communauté sur le package autonome Service Fabric pour Windows Server dans le [forum Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Ouvrez un ticket pour obtenir le [support professionnel Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  En savoir plus sur le support professionnel Microsoft [ici](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Vous pouvez également bénéficier du support pour ce package dans le cadre du [Support Premier Microsoft](https://support.microsoft.com/en-us/premier).
* Pour plus d’informations, consultez [Options de support d’Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
* Pour collecter des journaux à des fins de support, exécutez le [collecteur de journaux de Service Fabric autonome](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Télécharger le package Service Fabric pour Windows Server
Pour créer le cluster, utilisez le package Service Fabric pour Windows Server (Windows Server 2012 R2 et versions ultérieures) disponible ici : <br>
[Lien de téléchargement - Package autonome Service Fabric - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Plus d’informations sur le contenu du package [ici](service-fabric-cluster-standalone-package-contents.md).

Le package de runtime Service Fabric est automatiquement téléchargé lors de la création du cluster. En cas de déploiement à partir d’un ordinateur non connecté à Internet, téléchargez le package de runtime hors bande à partir d’ici : <br>
[Lien de téléchargement - Runtime Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Recherchez des exemples de configuration de cluster autonome sous : <br>
[Exemples de configuration de cluster autonome](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Création du cluster
Service Fabric peut être déployé vers un cluster de développement avec un ordinateur à l’aide du fichier *ClusterConfig.Unsecure.DevCluster.json* inclus dans les [exemples](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Décompressez le package autonome sur votre ordinateur, copiez l’exemple de fichier de configuration sur l’ordinateur local, puis exécutez le script *CreateServiceFabricCluster.ps1* via une session PowerShell administrateur, à partir du dossier du package autonome :
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Étape 1A : Créer un cluster de développement local non sécurisé
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Consultez la section Configuration de l’environnement sous [Planifier et préparer le déploiement de cluster](service-fabric-cluster-standalone-deployment-preparation.md) pour obtenir plus d’informations de dépannage.

Si vous avez fini d’exécuter les scénarios de développement, vous pouvez supprimer le cluster Service Fabric de l’ordinateur en vous reportant aux étapes de la section « [Supprimer un cluster](#removecluster_anchor) ». 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Étape 1B : Créer un cluster de plusieurs ordinateurs
Après avoir effectué les tâches de planification et de préparation détaillées par le lien ci-dessous, vous êtes prêt à créer votre cluster de production à l’aide de votre fichier de configuration de cluster. <br>
[Planifier et préparer le déploiement de clusters](service-fabric-cluster-standalone-deployment-preparation.md)

1. Validez le fichier de configuration que vous avez écrit en exécutant le script *TestConfiguration.ps1* à partir du dossier du package autonome :  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Un résultat similaire à ce qui suit s’affiche normalement. Si le champ du bas « Passed » (Transmis) est retourné avec la valeur « True », les contrôles de validité ont réussi et le cluster semble être déployable à l’aide de la configuration d’entrée.

    ```
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Créez le cluster : exécutez le script *CreateServiceFabricCluster.ps1* pour déployer le cluster Service Fabric sur chaque ordinateur dans la configuration. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Les traces de déploiement sont écrites sur la machine virtuelle/l’ordinateur où vous avez exécuté le script PowerShell CreateServiceFabricCluster.ps1. Elles ne se trouvent pas dans le sous-dossier DeploymentTraces, situé dans le répertoire à partir duquel le script a été exécuté. Pour voir si Service Fabric a été déployé correctement sur un ordinateur, recherchez les fichiers installés dans le répertoire FabricDataRoot, comme indiqué dans la section FabricSettings du fichier de configuration du cluster (par défaut c:\ProgramData\SF). De même, vous pouvez consulter l’exécution des processus FabricHost.exe et Fabric.exe dans le Gestionnaire des tâches.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Étape 1C : Créer un cluster hors connexion (déconnecté d’Internet)
Le package de runtime Service Fabric est automatiquement téléchargé lors de la création du cluster. Lorsque vous déployez un cluster sur des ordinateurs qui ne sont pas connectés à Internet, vous devrez télécharger le package de runtime Service Fabric séparément et fournir le chemin d’accès à celui-ci lors de la création du cluster.
Le package de runtime peut être téléchargé séparément, à partir d’un autre ordinateur connecté à Internet, en cliquant sur le [lien de téléchargement - Runtime Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copiez le package de runtime à l’endroit à partir duquel vous déployez le cluster hors connexion et créez le cluster en exécutant `CreateServiceFabricCluster.ps1` avec le paramètre `-FabricRuntimePackagePath` inclus, comme indiqué ci-dessous : 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
où `.\ClusterConfig.json` et `.\MicrosoftAzureServiceFabric.cab` correspondent, respectivement, aux chemins d’accès à la configuration du cluster et au fichier .cab de runtime.


### <a name="step-2-connect-to-the-cluster"></a>Étape 2 : Se connecter au cluster
Pour vous connecter à un cluster sécurisé, consultez [Service fabric connect to secure cluster](service-fabric-connect-to-secure-cluster.md) (Connexion à un cluster sécurisé avec Service Fabric).

Exécutez la commande PowerShell suivante pour vous connecter à un cluster non sécurisé :

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Exemple :
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Étape 3 : Afficher Service Fabric Explorer
Désormais, vous pouvez vous connecter au cluster avec Service Fabric Explorer directement à partir de l’un des ordinateurs avec http://localhost:19080/Explorer/index.html ou à distance avec http://<*adresse_IP_ordinateur*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Ajouter et supprimer des nœuds
Vous pouvez ajouter des nœuds à votre cluster Service Fabric autonome ou en supprimer en fonction de l’évolution de vos besoins. Consultez l’article [Ajouter ou supprimer des nœuds à/d’un cluster Service Fabric autonome](service-fabric-cluster-windows-server-add-remove-nodes.md) pour obtenir des instructions détaillées.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Supprimer un cluster
Pour supprimer un cluster, exécutez le script PowerShell *RemoveServiceFabricCluster.ps1* à partir du dossier de package et transmettez le chemin du fichier de configuration JSON. Vous pouvez éventuellement spécifier un emplacement pour le journal de la suppression.

Ce script peut être exécuté sur n’importe quel ordinateur disposant d’un accès administrateur à tous les ordinateurs qui sont répertoriés en tant que nœuds dans le fichier de configuration du cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Données de télémétrie recueillies et comment se désabonner
Par défaut, le produit collecte les données de télémétrie sur l’utilisation de Service Fabric en vue de l’améliorer. L’outil d’analyse des meilleures pratiques qui s’exécute dans le cadre de l’installation contrôle la connectivité à [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Si le service n’est pas joignable, la configuration échoue, sauf si vous vous désabonnez de la télémétrie.

1. Le pipeline de télémétrie essaye de charger les données suivantes sur [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) une fois par jour. Ce meilleur téléchargement n’a aucun impact sur la fonctionnalité de cluster. Les données de télémétrie sont envoyées uniquement à partir du nœud qui exécute le service Failover Manager principal. Aucun autre nœud n’envoie des données de télémétrie.
2. La télémétrie se compose des éléments suivants :

* Nombre de services
* Nombre de ServiceTypes
* Nombre de Applications
* Nombre de ApplicationUpgrades
* Nombre de FailoverUnits
* Nombre de InBuildFailoverUnits
* Nombre de UnhealthyFailoverUnits
* Nombre de Replicas
* Nombre de InBuildReplicas
* Nombre de StandByReplicas
* Nombre de OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Nombre de nœuds
* IsContextComplete : True/False
* ClusterId : GUID généré de manière aléatoire pour chaque cluster
* ServiceFabricVersion
* Adresse IP de la machine virtuelle ou de l’ordinateur à partir de laquelle/duquel les données de télémétrie sont téléchargées

Pour désactiver les données de télémétrie, ajoutez ce qui suit à *properties* dans votre configuration de cluster : *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Fonctionnalités préliminaires incluses dans ce package
Aucune.


> [!NOTE]
> À partir de la nouvelle [version mise à la disposition générale du cluster autonome pour Windows Server (version 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), vous pouvez mettre à niveau votre cluster vers des versions ultérieures, manuellement ou automatiquement. Reportez-vous au document [Mettre à niveau une version autonome du cluster Service Fabric](service-fabric-cluster-upgrade-windows-server.md) pour plus d’informations.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [Déployer et supprimer des applications avec PowerShell](service-fabric-deploy-remove-applications.md)
* [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md)
* [Ajouter ou supprimer des nœuds à/d’un cluster Service Fabric autonome](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Mettre à niveau une version autonome du cluster Service Fabric](service-fabric-cluster-upgrade-windows-server.md)
* [Créer un cluster Service Fabric autonome avec des machines virtuelles Azure Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows](service-fabric-windows-cluster-windows-security.md)
* [Sécuriser un cluster autonome sur Windows à l’aide de certificats X509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
