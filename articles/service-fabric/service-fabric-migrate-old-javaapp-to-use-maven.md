---
title: "Migrer du Kit de développement logiciel (SDK) Java vers Maven - Mettre à jour les anciennes applications Java d’Azure Service Fabric pour utiliser Maven | Microsoft Docs"
description: "Mettez à jour les anciennes applications Java qui utilisaient le Kit de développement logiciel (SDK) Java Service Fabric, pour extraire des dépendances Java Service Fabric dans Maven. Une fois l’installation terminée, vos anciennes applications Java devraient pouvoir générer."
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: rapatchi
ms.openlocfilehash: 7eac05c859440e868e1a207f0ba0d1bdf41e6e6b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Mettez à jour votre précédente application Java Service Fabric pour extraire des bibliothèques Java dans Maven
Nous avons récemment déplacé des binaires Java du Kit de développement logiciel (SDK) Java Service Fabric vers un hébergement Maven. Vous pouvez dorénavant utiliser le référentiel **mavencentral** pour extraire les dépendances Java récentes de Service Fabric. Ce démarrage rapide vous aide à mettre à jour vos applications Java existantes, créées plus tôt pour qu’elles fonctionnent avec le Kit de développement logiciel (SDK) Java Service Fabric, à l’aide du modèle Yeoman ou Eclipse, afin de les rendre compatibles avec le build basé sur Maven.

## <a name="prerequisites"></a>Prérequis
1. Vous devez tout d’abord désinstaller le Kit de développement logiciel (SDK) Java existant.

  ```bash
  sudo dpkg -r servicefabricsdkjava
  ```
2. Installez la dernière interface de ligne de commande Service Fabric en suivant les instructions disponibles [sur cette page](service-fabric-cli.md).

3. Pour générer et travailler avec les applications Java Service Fabric, vérifiez que JDK 1.8 et Gradle sont installés. Le cas échéant, exécutez les commandes suivantes pour installer JDK 1.8 (openjdk-8-jdk) et Gradle :

 ```bash
 sudo apt-get install openjdk-8-jdk-headless
 sudo apt-get install gradle
 ```
4. Mettez à jour les scripts d’installation et de désinstallation de votre application pour utiliser la nouvelle interface de ligne de commande Service Fabric en suivant les instructions disponibles [sur cette page](service-fabric-application-lifecycle-sfctl.md). Si besoin, vous pouvez vous aider de nos [exemples](https://github.com/Azure-Samples/service-fabric-java-getting-started) de prise en main.

>[!TIP]
> Après avoir désinstallé le Kit de développement (SDK) Java Service Fabric, Yeoman ne fonctionne plus. Suivez les conditions préalables détaillées [sur cette page](service-fabric-create-your-first-linux-application-with-java.md) pour faire fonctionner correctement le générateur de modèle Java Yeoman de Service Fabric.

## <a name="service-fabric-java-libraries-on-maven"></a>Bibliothèques Java Service Fabric sur Maven
Les bibliothèques Java Service Fabric ont été hébergées dans Maven. Vous pouvez ajouter les dépendances dans les éléments ``pom.xml`` ou ``build.gradle`` de vos projets pour utiliser les bibliothèques Java Service Fabric à partir du référentiel **mavenCentral**.

### <a name="actors"></a>Acteurs

Assistance Reliable Actor de Service Fabric pour votre application.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.10.0'
  }
  ```

### <a name="services"></a>Services

Assistance des services sans état de Service Fabric pour votre application.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.10.0'
  }
  ```

### <a name="others"></a>Autres
#### <a name="transport"></a>Transport

Assistance de la couche transport pour application Java Service Fabric. Il est inutile d’ajouter explicitement cette dépendance à votre service Reliable Actor ou à vos Applications de service, à moins d’effectuer la programmation au niveau de la couche transport.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.10.0'
  }
  ```

#### <a name="fabric-support"></a>Assista,ce Fabric

Assistance du niveau système pour Service Fabric, qui communique avec le runtime Service Fabric. Vous n’avez pas besoin d’ajouter explicitement cette dépendance à vos applications Reliable Actor ou Service Fabric. Elle est extraite automatiquement depuis Maven, lorsque vous incluez les autres dépendances ci-dessus.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.10.0'
  }
  ```


## <a name="migrating-service-fabric-stateless-service"></a>Migration des services sans état Service Fabric

Pour être en mesure de générer les services sans état Java Service Fabric à l’aide des dépendances de Service Fabric extraites depuis Maven, vous devez mettre à jour le fichier ``build.gradle`` qui se trouve dans Service Fabric. Auparavant, il fallait procéder comme suit :
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```
Maintenant, pour extraire les dépendances depuis Maven, le fichier ``build.gradle`` **mis à jour** disposerait des parties correspondantes suivantes :
```
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```
Pour vous faire une idée générale de ce à quoi ressemblerait le script du build dans un service sans état Java Service Fabric, référez-vous à n’importe quel exemple disponible dans nos exemples de prise en main. Voici le fichier [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/Services/EchoServer/EchoServer1.0/EchoServerService/build.gradle) pour l’exemple EchoServer.

## <a name="migrating-service-fabric-actor-service"></a>Migration du service d’acteur Service Fabric

Pour être en mesure de générer le service d’acteur d’une application Java Service Fabric à l’aide des dépendances de Service Fabric extraites depuis Maven, vous devez mettre à jour le fichier ``build.gradle`` qui se trouve dans le package d’interface et le package Service Fabric. Si vous avez un package TestClient, mettez-le aussi à jour. Pour l’acteur ``Myactor``, voici les emplacements que vous devez mettre à jour :
```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Mise à jour du script de build pour le projet d’interface

Auparavant, il fallait procéder comme suit :
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```
Maintenant, pour extraire les dépendances depuis Maven, le fichier ``build.gradle`` **mis à jour** disposerait des parties correspondantes suivantes :
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Mise à jour du script de build pour le projet d’acteur

Auparavant, il fallait procéder comme suit :
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```
Maintenant, pour extraire les dépendances depuis Maven, le fichier ``build.gradle`` **mis à jour** disposerait des parties correspondantes suivantes :
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Mise à jour du script de build pour le projet de client test

Les modifications sont semblables à celles effectuées dans la section précédente pour le projet d’acteur. Auparavant, le script Gradle ressemblait à :
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
      compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
  destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```
Maintenant, pour extraire les dépendances depuis Maven, le fichier ``build.gradle`` **mis à jour** disposerait des parties correspondantes suivantes :
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Créer et déployer votre première application Java Service Fabric sous Linux à l’aide de Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Créer et déployer votre première application Java Service Fabric sous Linux à l’aide du plug-in Service Fabric pour Eclipse](service-fabric-get-started-eclipse.md)
* [Interagir avec les clusters Service Fabric à l’aide de l’interface de ligne de commande Service Fabric](service-fabric-cli.md)
