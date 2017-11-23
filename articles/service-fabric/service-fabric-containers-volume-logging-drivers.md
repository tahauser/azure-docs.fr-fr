---
title: "Version préliminaire de Docker Compose dans Azure Service Fabric | Microsoft Docs"
description: "Azure Service Fabric accepte le format Docker Compose pour vous permettre d’orchestrer facilement des conteneurs existants à l’aide de Service Fabric. Cette prise en charge est actuellement en mode préliminaire."
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
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 955f84e5656bbf568234cbaf69faa4dd0a741206
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>Utilisation de plug-ins de volume et de pilotes de journalisation dans votre conteneur
Service Fabric prend en charge la spécification de [plug-ins de volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) et de [pilotes de journalisation de Docker](https://docs.docker.com/engine/admin/logging/overview/) pour votre service de conteneur.  Vous pouvez ainsi rendre vos données persistantes dans [Azure Files](https://azure.microsoft.com/en-us/services/storage/files/) même si vous déplacez ou redémarrez votre conteneur sur un autre hôte.

Il n’y a pour l’instant que des pilotes de volume pour les conteneurs Linux, comme indiqué ci-dessous.  Si vous utilisez des conteneurs Windows, il est possible de mapper un volume à un [partage SMB3](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) Azure Files sans pilote de volume à l’aide de la dernière version (1709) de Windows Server. Vous devez pour cela mettre à jour vos machines virtuelles dans votre cluster vers Windows Server version 1709.


## <a name="install-volumelogging-driver"></a>Installer le pilote de journalisation/volume

Si le pilote de journalisation/volume Docker n’est pas installé sur la machine, installez-le manuellement en intégrant RDP/SSH à la machine, par le biais d’un [script de démarrage VMSS](https://azure.microsoft.com/en-us/resources/templates/201-vmss-custom-script-windows/) ou à l’aide d’un script [SetupEntryPoint](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-model#describe-a-service). En choisissant l’une des méthodes indiquées, vous pouvez écrire un script pour installer le [pilote de volume Docker pour Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) :


```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>Spécifier le plug-in ou le pilote dans le manifeste
Les plug-ins sont spécifiés dans le manifeste de l’application, comme indiqué dans le manifeste suivant :

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
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
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
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

Dans l’exemple précédent, l’étiquette `Source` pour le `Volume` fait référence au dossier source. Le dossier source peut être un dossier de la machine virtuelle qui héberge les conteneurs ou un magasin distant persistant. L’étiquette `Destination` correspond à l’emplacement auquel est mappé l’élément `Source` dans le conteneur en cours d’exécution.  Votre destination ne peut donc pas être un emplacement existant dans votre conteneur.

Lorsque vous spécifiez un plug-in de volume, Service Fabric crée automatiquement le volume à l’aide des paramètres spécifiés. La balise `Source` est le nom du volume, et la balise `Driver` spécifie le plug-in de pilote de volume. Des options peuvent être spécifiées à l’aide de la balise `DriverOption`, comme indiqué dans l’extrait de code suivant :

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Si un pilote de journalisation Docker est spécifié, il est nécessaire de déployer des agents (ou conteneurs) pour gérer les journaux dans le cluster.  La balise `DriverOption` peut également être utilisée pour spécifier les options de pilote de journal.

Consultez les articles suivants pour déployer des conteneurs dans un cluster Service Fabric :


[Déployer un conteneur sur Service Fabric](service-fabric-deploy-container.md)

