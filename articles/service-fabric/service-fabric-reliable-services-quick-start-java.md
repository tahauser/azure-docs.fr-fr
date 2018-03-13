---
title: "Créer votre premier microservice Azure fiable en Java | Microsoft Docs"
description: "Introduction à la création d'une application Microsoft Azure Service Fabric avec des services avec et sans état."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: e885a482edcba48c18e425c54f4acc28ee650ddd
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="get-started-with-reliable-services"></a>Prise en main de Reliable Services
> [!div class="op_single_selector"]
> * [C# sur Windows](service-fabric-reliable-services-quick-start.md)
> * [Java sur Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Cet article explique les notions de base d’Azure Service Fabric Reliable Services et vous guide pas à pas dans la création et le déploiement d’une application Reliable Service simple écrite en Java. Cette vidéo Microsoft Virtual Academy vous montre également comment créer un service Reliable sans état :<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Installation et configuration
Avant de commencer, assurez-vous que l’environnement de développement Service Fabric est configuré sur votre ordinateur.
Si vous devez le configurer, référez-vous à l’article sur la [prise en main sur Mac](service-fabric-get-started-mac.md) ou sur la [prise en main sur Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concepts de base
Pour prendre en main Reliable Services, il vous suffit de comprendre quelques concepts de base :

* **Type de service** : il s’agit de l’implémentation de votre service. Elle est définie par la classe que vous écrivez qui étend `StatelessService` et tout autre code ou dépendances utilisés ici, ainsi qu’un nom et un numéro de version.
* **Instance de service nommée** : pour exécuter votre service, vous créez des instances nommées de votre type de service, de la même manière que vous créez des instances d’objet d’un type de classe. Les instances de service sont en fait des instanciations d’objet de votre classe de service que vous écrivez.
* **Hôte de service** : les instances de service nommées que vous créez doivent s’exécuter au sein d’un ordinateur hôte. L’hôte de service est simplement un processus dans lequel les instances de votre service peuvent s’exécuter.
* **Inscription du service** : l’inscription rassemble tous les éléments. Le type de service doit être inscrit auprès du runtime Service Fabric dans un hôte de service pour autoriser Service Fabric à créer des instances de ce type à exécuter.  

## <a name="create-a-stateless-service"></a>Création d'un service sans état
Commencez par créer une application Service Fabric. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur Yeoman qui assure la génération de modèles automatique pour une application Service Fabric avec un service sans état. Commencez par exécuter la commande Yeoman suivante :

```bash
$ yo azuresfjava
```

Suivez les instructions pour créer un **service sans état fiable**. Pour ce didacticiel, nommez l’application « HelloWorldApplication » et le service « HelloWorld ». Le résultat comprend des répertoires pour `HelloWorldApplication` et `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Inscription du service
Les types de service doivent être inscrits auprès du runtime Service Fabric. Le type de service est défini dans le fichier `ServiceManifest.xml` et votre classe de service qui implémente `StatelessService`. L’inscription du service est réalisée dans le point d’entrée principal du processus. Dans cet exemple, le point d’entrée principal du processus est `HelloWorldServiceHost.java` :

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Mettre en œuvre le service

Ouvrez **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Cette classe définit le type de service et peut exécuter n’importe quel code. L'API de service fournit deux points d'entrée pour votre code :

* Une méthode de point d’entrée de durée indéterminée, appelée `runAsync()`, avec laquelle vous pouvez commencer l’exécution de toute charge de travail, y compris les charges de travail de calcul de longue durée.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Un point d’entrée de communication où vous pouvez connecter la pile de communication de votre choix. C’est là que vous pouvez commencer à recevoir des demandes des utilisateurs et des autres services.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Dans ce didacticiel, nous nous concentrons sur la méthode de point d’entrée `runAsync()`. C’est là que vous pouvez commencer immédiatement à exécuter votre code.

### <a name="runasync"></a>RunAsync
La plateforme appelle cette méthode quand une instance d’un service est placée et prête à être exécutée. Pour un service sans état, cela signifie simplement le moment où l’instance de service est ouverte. Un jeton d'annulation est fourni pour coordonner lorsque l'instance de service doit être fermée. Dans Service Fabric, ce cycle d’ouverture/fermeture d’une instance de service peut se produire plusieurs fois au cours de la durée de vie de votre service dans son ensemble. Il existe diverses raisons à cela, notamment :

* Le système déplace vos instances de service à des fins d’équilibrage des ressources.
* Des erreurs surviennent dans votre code.
* L’application ou le système sont mis à niveau.
* Le matériel sous-jacent tombe en panne.

Cette orchestration est gérée par Service Fabric afin de maintenir une haute disponibilité et un équilibrage correct pour votre service.

`runAsync()` ne doit pas se bloquer de façon synchrone. Votre implémentation de runAsync doit renvoyer un objet CompletableFuture pour permettre au runtime de continuer si votre charge de travail a besoin d’implémenter une tâche longue qui doit être effectuée à l’intérieur de l’objet CompletableFuture.

#### <a name="cancellation"></a>Annulation
L'annulation de votre charge de travail est un effort conjoint orchestré par le jeton d'annulation fourni. Le système attend la fin de la tâche (suite à sa réussite, à son annulation ou à une défaillance) avant de poursuivre. Il est important de respecter le jeton d’annulation, de terminer le travail et de quitter `runAsync()` aussi rapidement que possible quand le système demande une annulation. L’exemple suivant montre comment gérer un événement d’annulation :

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

Dans cet exemple de service sans état, le décompte est stocké dans une variable locale. Mais comme il s’agit d’un service sans état, la valeur stockée existe uniquement pendant le cycle de vie actuel de son instance de service. Lorsque le service se déplace ou redémarre, la valeur est perdue.

## <a name="create-a-stateful-service"></a>Création d'un service avec état
Service Fabric introduit un nouveau type de service qui est avec état. Un service avec état peut conserver un état de façon fiable dans le service lui-même, colocalisé avec le code qui l’utilise. L’état est rendu hautement disponible par Service Fabric sans avoir besoin de le conserver dans un magasin externe.

Pour convertir une valeur de compteur sans état en valeur hautement disponible et persistante, même quand le service se déplace ou redémarre, vous avez besoin d’un service avec état.

Dans le même répertoire que l’application HelloWorld, vous pouvez ajouter un nouveau service en exécutant la commande `yo azuresfjava:AddService`. Choisissez Reliable Stateful Service (Service avec état fiable) pour votre framework et nommez ce service « HelloWorldStateful ». 

Votre application doit maintenant contenir deux services : le service sans état HelloWorld et le service avec état HelloWorldStateful.

Un service avec état a les mêmes points d'entrée qu'un service sans état. La principale différence est la disponibilité d’un fournisseur d’état qui peut stocker l’état de manière fiable. Service Fabric est fourni avec une implémentation de fournisseur d’état appelée Reliable Collections (Collections fiables), qui vous permet de créer des structures de données répliquées via le Reliable State Manager (gestionnaire d’état fiable). Un Reliable Service avec état utilise ce fournisseur d’état par défaut.

Ouvrez HelloWorldStateful.java dans **HelloWorldStateful -> src** qui contient la méthode RunAsync suivante :

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` fonctionne de la même façon dans les services avec ou sans état. Toutefois, dans un service avec état, la plateforme exécute un travail supplémentaire en votre nom avant d’exécuter `RunAsync()`. Ce travail peut inclure de veiller à ce que le Gestionnaire d’état fiable et les Reliable Collections soient prêts à l’emploi.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Reliable Collections et Gestionnaire d’état fiable
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) est une implémentation de dictionnaire qui peut servir à stocker de façon fiable l’état dans le service. Avec Service Fabric et les Reliable Hashmaps (tables de hachage fiables), vous pouvez stocker des données directement dans votre service sans avoir besoin d’un magasin persistant externe. Les tables de hachage fiables rendent vos données hautement disponibles. Pour ce faire, Service Fabric crée et gère plusieurs *réplicas* de votre service pour vous. Il fournit également une API qui élimine la complexité de la gestion de ces réplicas et leurs transitions d’état.

Les collections fiables peuvent stocker n’importe quel type Java, dont vos types personnalisés, avec quelques inconvénients :

* Service Fabric rend votre état hautement disponible en *répliquant* l’état sur tous les nœuds, et la table de hachage fiable stocke vos données sur le disque local de chaque réplica. Cela signifie que tout ce qui est stocké dans les tables de hachage fiables doit être *sérialisable*. 
* Les objets sont répliqués à des fins de haute disponibilité quand vous validez des transactions sur des tables de hachage fiables. Les objets stockés dans les tables de hachage fiables sont conservés dans la mémoire locale de votre service. Cela signifie que vous avez une référence locale à l’objet.
  
   Il est important de ne pas muter les instances locales de ces objets sans effectuer une opération de mise à jour sur la collection fiable dans une transaction. En effet, les modifications des instances locales d’objets ne sont pas répliquées automatiquement. Vous devez réinsérer l’objet dans le dictionnaire ou utiliser l’une des méthodes de *mise à jour* sur le dictionnaire.

Le gestionnaire d’état fiable gère les tables de hachage fiables pour vous. Vous pouvez simplement demander au Gestionnaire d’état fiable une collection fiable par son nom à tout moment et à tout emplacement dans votre service. Le Gestionnaire d’état fiable permet de récupérer une référence. Nous ne vous recommandons pas d’enregistrer les références aux instances de la collection fiable dans des variables ou propriétés de membre de classe. Vous devez particulièrement veiller à ce que la référence soit définie sur une instance pendant tout le cycle de vie du service. Ce travail, optimisé pour les visites répétées, est géré pour vous par le Gestionnaire d’état fiable.


### <a name="transactional-and-asynchronous-operations"></a>Opérations transactionnelles et asynchrones
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Les opérations sur les tables de hachage fiables sont asynchrones. Ceci est dû au fait que les opérations d’écriture avec les Reliable Collections exécutent des opérations E/S pour répliquer et faire persister les données sur le disque.

Les opérations des tables de hachage fiables sont *transactionnelles*. Ainsi, vous pouvez conserver un état cohérent entre plusieurs tables de hachage fiables et opérations. Vous pouvez, par exemple, obtenir un élément de travail d’un Reliable Dictionary (dictionnaire fiable), effectuer une opération sur celui-ci et enregistrer le résultat dans une autre table de hachage fiable, le tout dans une transaction unique. Ces opérations sont traitées comme une seule opération atomique, ce qui garantit soit son succès total, soit son échec total. Si une erreur se produit une fois que vous avez retiré l’élément de la file d’attente, mais avant d’enregistrer le résultat, la transaction entière est annulée et l’élément reste dans la file d’attente à des fins de traitement.


## <a name="run-the-application"></a>Exécution de l'application

La génération de modèles automatique Yeoman inclut un script Gradle permettant de créer l’application et des scripts bash permettant le déploiement et la suppression de l’application. Pour exécuter l’application, commencez par créer l’application avec Gradle :

```bash
$ gradle
```

Cela génère un package d’application Service Fabric qui peut être déployé à l’aide de l’interface CLI Azure Service Fabric.

### <a name="deploy-with-service-fabric-cli"></a>Effectuer un déploiement à l’aide de l’interface CLI Service Fabric

Le script install.sh contient les commandes de l’interface CLI Service Fabric nécessaires pour déployer le package d’application. Exécutez le script install.sh pour déployer l’application.

```bash
$ ./install.sh
```

## <a name="next-steps"></a>Étapes suivantes

* [Prise en main de l’interface de ligne de commande Service Fabric](service-fabric-cli.md)
