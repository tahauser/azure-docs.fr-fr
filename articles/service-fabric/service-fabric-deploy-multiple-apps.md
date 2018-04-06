---
title: Déployer une application Node.js qui utilise MongoDB sur Azure Service Fabric | Documents Microsoft
description: Procédure détaillée indiquant comment empaqueter plusieurs exécutables invités à déployer sur un cluster Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4987cc271a3e3d8a1a69c4c7fbd213d73ca2eb96
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-multiple-guest-executables"></a>Déploiement de plusieurs exécutables invités
Cet article montre comment empaqueter et déployer plusieurs exécutables sur Azure Service Fabric. Pour créer et déployer un package Service Fabric, lisez l’article [Déploiement d’un exécutable invité dans Service Fabric](service-fabric-deploy-existing-app.md).

Bien que cette procédure montre pas à pas comment déployer une application avec un serveur frontal Node.js utilisant MongoDB comme magasin de données, vous pouvez appliquer les étapes à n’importe quelle application ayant des dépendances dans une autre application.   

Vous pouvez utiliser Visual Studio pour générer le package d’application qui contient plusieurs fichiers exécutables invités. Voir [Utilisation de Visual Studio pour empaqueter une application existante](service-fabric-deploy-existing-app.md). Après avoir ajouté le premier exécutable invité, cliquez avec le bouton droit sur le projet d’application, puis sélectionnez **Ajouter -> nouveau service Service Fabric** pour ajouter le second projet exécutable invité à la solution. Remarque : si vous choisissez d’établir un lien vers la source dans le projet Visual Studio, assurez-vous lors de la création de la solution Visual Studio que votre package d’application est à jour avec les modifications de la source. 

## <a name="samples"></a>Exemples
* [Exemple pour empaqueter et déployer un fichier exécutable invité](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemple de deux exécutables invités (C# et nodejs) communiquant via le service d’attribution de noms à l’aide de REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Empaqueter manuellement l’application exécutable invitée multiple
Vous pouvez également empaqueter manuellement l’exécutable invité. Pour l’empaquetage manuel, cet article utilise l’outil d’empaquetage Service Fabric, disponible à l’adresse [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Empaquetage de l'application Node.js
Cet article part du principe que Node.js n’est pas installé sur les nœuds du cluster Service Fabric. Par conséquent, vous devez ajouter Node.exe dans le répertoire racine de votre application de nœud avant l’empaquetage. La structure de répertoires de l’application Node.js (avec la structure web Express et le moteur de création de modèles Jade) doit ressembler à ce qui suit :

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Ensuite, vous créez un package d’application pour l’application Node.js. Le code suivant crée un package d'application Service Fabric qui contient l'application Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Voici une description des paramètres utilisés :

* **/source** : pointe vers le répertoire de l’application qui doit être empaquetée.
* **/target** : définit le répertoire dans lequel le package doit être créé. Ce répertoire doit être différent du répertoire source.
* **/appname** : définit le nom d’application de l’application existante. Il est important de comprendre que ce nom devient le nom du service dans le manifeste, et non celui de l’application Service Fabric.
* **/exe** : définit l’exécutable que Service Fabric doit démarrer, dans ce cas `node.exe`.
* **/ma** : définit l’argument utilisé pour démarrer l’exécutable. Node.js n’étant pas installé, Service Fabric doit démarrer le serveur web Node.js en exécutant `node.exe bin/www`.  `/ma:'bin/www'` indique l’outil d’empaquetage pour utiliser `bin/www` comme argument pour node.exe.
* **/AppType** : définit le nom du type de l’application Service Fabric.

Si vous accédez au répertoire spécifié dans le paramètre /target, vous constatez que l’outil a créé un package Service Fabric entièrement fonctionnel, comme illustré ci-dessous :

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Le fichier ServiceManifest.xml généré contient maintenant une section qui décrit comment le serveur web Node.js doit être démarré, comme l’illustre l’extrait de code ci-dessous :

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Dans cet exemple, le serveur web Node.js écoute le port 3000. Vous devez donc mettre à jour les informations du point de terminaison dans le fichier ServiceManifest.xml comme indiqué ci-dessous.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Empaquetage de l'application MongoDB
Une fois que vous avez créé le package de l’application Node.js, vous pouvez poursuivre et créer un package de MongoDB. Comme nous l’avons mentionné plus haut, les étapes à suivre ne sont pas propres à Node.js et MongoDB. Elles s’appliquent en fait à toutes les applications destinées à être regroupées en une seule application Service Fabric.  

Pour créer un package de MongoDB, vous devez veiller à créer un package Mongod.exe et Mongo.exe. Les deux fichiers binaires se trouvent dans le répertoire `bin` de votre répertoire d’installation de MongoDB. La structure de répertoires est similaire à celle présentée ci-dessous.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric doit démarrer MongoDB avec une commande semblable à celle ci-dessous. Vous devez donc utiliser le paramètre `/ma` lors de l’empaquetage de MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Les données ne sont pas conservées en cas de défaillance de nœud si vous placez le répertoire de données MongoDB sur le répertoire local du nœud. Vous devez utiliser un stockage durable ou implémenter un jeu de réplicas MongoDB pour éviter de perdre des données.  
>
>

Dans PowerShell ou l’interface de commande, nous exécutons l’outil d’empaquetage avec les paramètres suivants :

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Pour ajouter MongoDB à votre package d’application Service Fabric, vous devez vous assurer que le paramètre /target pointe vers le répertoire qui contient déjà le manifeste de l’application, ainsi que l’application Node.js. Vous devez également veiller à utiliser le même nom ApplicationType.

Accédons au répertoire et examinons ce que l'outil a créé.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Comme vous pouvez le voir, l’outil a ajouté un nouveau dossier, MongoDB, au répertoire contenant les fichiers binaires de MongoDB. Si vous ouvrez le fichier `ApplicationManifest.xml` , vous constatez que le package contient désormais l’application Node.js et MongoDB. Le code suivant montre le contenu du manifeste d'application.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publication de l'application
La dernière étape consiste à publier l’application sur le cluster Service Fabric local à l’aide des scripts PowerShell ci-dessous :

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Une fois que l’application est publiée dans le cluster local, vous pouvez accéder à l’application Node.js sur le port entré dans le manifeste de service de l’application Node.js, par exemple http://localhost:3000.

Dans ce didacticiel, vous avez vu comment empaqueter facilement deux applications existantes en une application Service Fabric. Vous avez également découvert comment la déployer dans Service Fabric pour qu’elle tire parti de certaines fonctionnalités de Service Fabric, telles que la haute disponibilité et l’intégration du système de contrôle d’intégrité.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Ajout de plusieurs exécutables invités à une application existante à l’aide de Yeoman sous Linux

Pour ajouter un autre service à une application déjà créée à l’aide de `yo`, procédez comme suit : 
1. Accédez au répertoire à la racine de l’application existante.  Par exemple, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` est l’application créée par Yeoman.
2. Exécutez `yo azuresfguest:AddService` et fournissez les informations nécessaires.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le déploiement de conteneurs avec [Service Fabric et vue d’ensemble des conteneurs](service-fabric-containers-overview.md)
* [Exemple pour empaqueter et déployer un fichier exécutable invité](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemple de deux exécutables invités (C# et nodejs) communiquant via le service d’attribution de noms à l’aide de REST](https://github.com/Azure-Samples/service-fabric-containers)
