---
title: Comment conteneuriser les microservices Azure Service Fabric (préversion)
description: Azure Service Fabric a ajouté de nouvelles fonctionnalités, qui vous permettent de conteneuriser vos microservices Service Fabric. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: anmolah
editor: anmolah
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/04/2017
ms.author: anmola
ms.openlocfilehash: e66e488d8e547e828c014b105a816a14726e5005
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-containerize-your-service-fabric-reliable-services-and-reliable-actors-preview"></a>Comment conteneuriser les microservices Service Fabric Reliable Actors et Reliable Services (préversion)

Service Fabric prend en charge la conteneurisation des microservices Service Fabric (services basés sur Reliable Services et Reliable Actors). Pour en savoir plus, voir [Service Fabric et conteneurs](service-fabric-containers-overview.md).

Cette fonctionnalité est disponible en préversion. Cet article décrit les différentes étapes permettant d’assurer l’exécution de votre service au sein d’un conteneur.  

> [!NOTE]
> Cette fonctionnalité est disponible en préversion et n’est pas prise en charge dans les environnements de production. Actuellement, cette fonctionnalité fonctionne uniquement pour Windows. Pour exécuter des conteneurs, le cluster doit être en cours d’exécution sur Windows Server 2016 avec des conteneurs.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Étapes de conteneurisation de votre application Service Fabric

1. Ouvrez l’application Service Fabric dans Visual Studio.

2. Ajoutez la classe [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) à votre projet. Le code de cette classe correspond à une application auxiliaire qui gère le chargement correct des binaires du runtime Service Fabric dans l’application, lorsqu’elle est exécutée au sein d’un conteneur.

3. Pour chaque package de code que vous souhaitez conteneuriser, initialisez le chargeur au point d’entrée du programme. Ajoutez le constructeur statique indiqué dans l’extrait de code suivant à votre fichier de point d’entrée du programme.

  ```csharp
  namespace MyApplication
  {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
  ```

4. Générez et [créez un package](service-fabric-package-apps.md#Package-App) de votre projet. Pour générer et créer un package, cliquez avec le bouton droit sur le projet d’application dans l’Explorateur de solutions, puis choisissez la commande **Package**.

5. Pour chaque package de code que vous voulez conteneuriser, exécutez le script PowerShell suivant : [CreateDockerPackage.ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1). Procédez comme suit :
  ```powershell
    $codePackagePath = 'Path to the code package to containerize.'
    $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
    $applicationExeName = 'Name of the ode package executable.'
    CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
 ```
  Ce script crée un dossier avec des artefacts Docker à l’emplacement $dockerPackageOutputDirectoryPath. Modifiez le fichier Dockerfile généré pour exposer les ports, exécuter les scripts de configuration, et ainsi de suite, selon vos besoins.

6. Vous devez ensuite [générer](service-fabric-get-started-containers.md#Build-Containers) et [envoyer (push)](service-fabric-get-started-containers.md#Push-Containers) votre package de conteneur Docker vers votre référentiel.

7. Modifiez les fichiers ApplicationManifest.xml et ServiceManifest.xml de manière à ajouter le mappage des ports aux hôtes, l’authentification des registres, les informations sur les référentiels et l’image de votre conteneur. Pour savoir comment modifier les manifestes, voir [Créer votre première application de conteneur Service Fabric sur Windows](service-fabric-get-started-containers.md). La définition du package de code dans le manifeste de service doit être remplacée par l’image de conteneur correspondante. N’oubliez pas de remplacer la valeur du paramètre EntryPoint par un type ContainerHost.

  ```xml
<!-- Code package is your service executable. -->
<CodePackage Name="Code" Version="1.0.0">
  <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
  </EntryPoint>
  <!-- Pass environment variables to your container: -->    
</CodePackage>
  ```

8. Ajoutez le mappage des ports aux hôtes dans le cas du point de terminaison du réplicateur et du service. Comme ces ports sont affectés tous les deux lors du runtime par Service Fabric, la valeur ContainerPort est définie sur zéro, afin que le port affecté soit utilisé pour le mappage.

 ```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
  </ContainerHostPolicies>
</Policies>
 ```

9. Pour tester cette application, vous devez la déployer sur un cluster qui exécute la version 5.7 ou plus. En outre, vous devez modifier et mettre à jour les paramètres de cluster pour activer cette fonctionnalité en préversion. Suivez les étapes de cet [article](service-fabric-cluster-fabric-settings.md) pour ajouter le paramètre représenté ensuite.
```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
```
10. Ensuite, [déployez](service-fabric-deploy-remove-applications.md) le package d’application modifié sur ce cluster.

Vous devez maintenant disposer d’une application Service Fabric en conteneur exécutant votre cluster.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’exécution des [conteneurs sur Service Fabric](service-fabric-get-started-containers.md).
* En savoir plus sur le [cycle de vie des applications](service-fabric-application-lifecycle.md) Service Fabric.
