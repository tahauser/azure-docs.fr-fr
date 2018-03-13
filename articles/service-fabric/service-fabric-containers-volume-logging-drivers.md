---
title: "Docker Compose dans Azure Service Fabric (préversion) | Microsoft Docs"
description: "Azure Service Fabric accepte le format Docker Compose afin de simplifier l’orchestration des conteneurs existants à l’aide de Service Fabric. La prise en charge de Docker Compose est actuellement en préversion."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 79b4700b0b0b6897c19117044d37623a2f6ea8df
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>Utiliser des plug-ins de volume et des pilotes de journalisation Docker dans votre conteneur
Azure Service Fabric prend en charge la spécification de [plug-ins de volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) et de [pilotes de journalisation Docker](https://docs.docker.com/engine/admin/logging/overview/) pour votre service de conteneur. Vous pouvez rendre vos données persistantes dans [Azure Files](https://azure.microsoft.com/services/storage/files/) même quand vous déplacez ou redémarrez votre conteneur sur un autre hôte.

Seuls les pilotes de volume pour les conteneurs Linux sont actuellement pris en charge. Si vous utilisez des conteneurs Windows, vous pouvez mapper un volume à un [partage SMB3](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) Azure Files sans pilote de volume. Pour ce mappage, mettez à jour vos machines virtuelles dans votre cluster vers la version Windows Server 1709 la plus récente.


## <a name="install-the-docker-volumelogging-driver"></a>Installer le pilote de volume/journalisation Docker

Si le pilote de volume/journalisation Docker n’est pas installé sur l’ordinateur, vous pouvez l’installer manuellement à l’aide des protocoles RDP/SSH. Vous pouvez effectuer l’installation avec ces protocoles par le biais d’un [script de démarrage de groupe de machines virtuelles identiques](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) ou d’un [script SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model#describe-a-service).

Voici un exemple de script permettant d’installer le [pilote de volume Docker pour Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) :

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

> [!NOTE]
> Windows Server 2016 Datacenter ne prend pas en charge le mappage de montages SMB à des conteneurs ([cette fonctionnalité est uniquement prise en charge sur Windows Server version 1709](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/container-storage)). Cette contrainte empêche le mappage de volume réseau et les pilotes de volume Azure Files sur les versions antérieures à 1709. 
>   


## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>Spécifier le plug-in ou le pilote dans le manifeste
Les plug-ins sont spécifiés dans le manifeste d’application comme suit :

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

La balise **Source** de l’élément **Volume** fait référence au dossier source. Le dossier source peut être un dossier de la machine virtuelle qui héberge les conteneurs ou un magasin distant persistant. La balise **Destination** correspond à l’emplacement auquel est mappé la **Source** dans le conteneur en cours d’exécution. Ainsi, la destination ne peut pas être un emplacement qui existe déjà dans votre conteneur.

Les paramètres d’application sont pris en charge pour les volumes comme indiqué dans l’extrait de code de manifeste précédent (recherchez `MyStoreVar` pour obtenir un exemple d’utilisation).

Quand vous spécifiez un plug-in de volume, Service Fabric crée automatiquement le volume à l’aide des paramètres spécifiés. La balise **Source** est le nom du volume, et la balise **Driver** spécifie le plug-in de pilote de volume. Vous pouvez spécifier les options à l’aide de la balise **DriverOption** comme suit :

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Si un pilote de journalisation Docker est spécifié, vous devez déployer des agents (ou conteneurs) pour gérer les journaux dans le cluster. Vous pouvez utiliser la balise **DriverOption** pour spécifier des options pour le pilote de journal.

## <a name="next-steps"></a>Étapes suivantes
Pour déployer des conteneurs sur un cluster Service Fabric, consultez l’article [Déployer un conteneur sur Service Fabric](service-fabric-deploy-container.md).
