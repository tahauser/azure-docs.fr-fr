---
title: Spécification de points de terminaison du service Service Fabric | Microsoft Docs
description: Comment décrire les ressources du point de terminaison dans un manifeste de service, y compris comment configurer des points de terminaison HTTPS
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: a028471871754c2b9c3981ec13f5788643675a77
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="specify-resources-in-a-service-manifest"></a>Spécifier des ressources dans un manifeste de service
## <a name="overview"></a>Vue d'ensemble
Le manifeste de service met les ressources à la disposition du service à déclarer/modifier sans changer le code compilé. Azure Service Fabric prend en charge la configuration des ressources des points de terminaison du service. L’accès aux ressources spécifiées dans le manifeste de service peut être contrôlé par le biais de la valeur SecurityGroup dans le manifeste de l’application. La déclaration des ressources permet de les modifier au moment du déploiement. Ainsi, le service n’a pas besoin d’introduire un nouveau mécanisme de configuration. La définition de schéma pour le fichier ServiceManifest.xml est installée avec le Kit de développement logiciel (SDK) Service Fabric et les outils sous *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Points de terminaison
Lorsqu’une ressource de point de terminaison est définie dans le manifeste de service, Service Fabric alloue les ports de la plage de ports d’application réservés lorsqu’un port n’est pas spécifié de manière explicite. Examinez, par exemple, le point de terminaison *ServiceEndpoint1* spécifié dans l’extrait de code du manifeste fourni après ce paragraphe. En outre, les services peuvent également demander un port spécifique d’une ressource. Les réplicas de service exécutés sur des nœuds de cluster différents peuvent être alloués à des numéros de ports différents, tandis que les réplicas d’un service exécutés sur le même nœud partagent le port. Les réplicas de service peuvent alors utiliser ces ports pour la réplication et pour écouter les demandes du client.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

S’il existe plusieurs packages de code dans un package de service unique, le package de code doit également être référencé dans la section **Points de terminaison**.  Par exemple, si **ServiceEndpoint2a** et **ServiceEndpoint2b** sont des points de terminaison provenant du même package de service faisant référence à des packages de code différents, le package de code correspondant à chaque point de terminaison est clarifié comme suit :

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Reportez-vous à [Configuration de services Reliable Services avec état](service-fabric-reliable-services-configuration.md) pour en savoir plus sur le référencement des points de terminaison à partir du fichier des paramètres de package de configuration (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemple : spécification d’un point de terminaison HTTP pour votre service
Le manifeste de service suivant définit une seule ressource de point de terminaison TCP et deux ressources de point de terminaison HTTP dans l’élément &lt;Ressources&gt;.

Les points de terminaison HTTP sont automatiquement répertoriés dans la liste de contrôle d’accès par Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemple : spécification d’un point de terminaison HTTPS pour votre service
Le protocole HTTPS assure l'authentification du serveur et est également utilisé pour le chiffrement des communications client-serveur. Pour activer HTTPS sur votre service Service Fabric, spécifiez le protocole dans la section *Ressources -> Points de terminaison -> Point de terminaison* du manifeste de service, comme indiqué plus haut pour le point de terminaison *ServiceEndpoint3*.

> [!NOTE]
> Il est impossible de modifier le protocole d’un service lors de la mise à niveau de l’application. En effet, il s’agit d’une modification avec rupture.
> 
> 

Voici un exemple ApplicationManifest à définir pour le protocole HTTPS. Vous devez fournir l’empreinte numérique de votre certificat. EndpointRef est une référence à EndpointResource dans ServiceManifest, pour lequel vous définissez le protocole HTTPS. Vous pouvez ajouter plusieurs EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Pour les clusters Linux, le **MY** stocke par défaut dans le dossier **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Écraser des points de terminaison dans ServiceManifest.xml

Dans ServiceManifest, ajoutez une section ResourceOverrides, qui sera la sœur de la section ConfigOverrides. Vous pouvez y spécifier le remplacement de la section Points de terminaison dans la section Ressources spécifiée dans le manifeste de service. Le remplacement des points de terminaison est pris en charge dans le runtime 5.7.217/SDK 2.7.217 et versions supérieures.

Pour pouvoir remplacer EndPoint dans ServiceManifest à l’aide d’ApplicationParameters, modifiez ainsi ApplicationManifest :

Dans la section ServiceManifestImport, ajoutez une nouvelle section « ResourceOverrides ».

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Dans Parameters, ajoutez le code ci-dessous :

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Lors du déploiement de l’application, vous pouvez à présent passer ces valeurs comme ApplicationParameters, par exemple :

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Remarque : Si les valeurs affectées à ApplicationParameters sont vides, on revient à la valeur par défaut fournie dans ServiceManifest pour le nom EndPointName correspondant.

Par exemple : 

Si, dans ServiceManifest, vous avez spécifié :

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Et que la valeur Port1 et Protocol1 des paramètres d’application est Null ou vide. Le port est toujours déterminé par ServiceFabric. Et Protocol a la valeur tcp.

Supposons que vous spécifiez une valeur incorrecte. Par exemple, pour Port, vous avez spécifié la valeur de chaîne « Foo » au lieu d’un entier.  La commande New-ServiceFabricApplication échouera avec une erreur : « The override parameter with name ’ServiceEndpoint1’ attribute ’Port1’ in section ’ResourceOverrides’ is invalid. The value specified is ’Foo’ and required is ’int’. » (Le paramètre de remplacement portant le nom « ServiceEndpoint1 » pour l’attribut « Port1 » dans la section « ResourceOverrides » n’est pas valide. La valeur spécifiée est « Foo » alors que le type « int » est requis.)
