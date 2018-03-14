---
title: "Déployer une application .NET dans un conteneur vers Azure Service Fabric | Microsoft Docs"
description: "Explique comment empaqueter une application .NET dans Visual Studio dans un conteneur Docker. Cette nouvelle application « conteneur » est ensuite déployée sur un cluster Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.openlocfilehash: cd1c3b063132ae549bfbf1e059667c5056c91046
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Déployer une application .NET dans un conteneur Windows vers Azure Service Fabric

Ce didacticiel explique comment déployer une application ASP.NET dans un conteneur Windows sur Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un projet Docker dans Visual Studio
> * Mettre en conteneur une application existante
> * Configurer l’intégration continue avec Visual Studio et VSTS

## <a name="prerequisites"></a>Prérequis

1. Installez [Docker CE pour Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) afin que vous puissiez exécuter des conteneurs sur Windows 10.
2. Familiarisez-vous avec [l’utilisation de Windows 10 Containers][link-container-quickstart].
3. Téléchargez l’exemple d’application [Fabrikam Fiber CallCenter][link-fabrikam-github].
4. Installez [Azure PowerShell][link-azure-powershell-install]
5. Installez l’[Extension Outils de livraison continue pour Visual Studio 2017][link-visualstudio-cd-extension]
6. Créez un [Abonnement Azure][link-azure-subscription] et un [Compte Visual Studio Team Services][link-vsts-account]. 
7. [Créez un cluster sur Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="create-a-cluster-on-azure"></a>Créer un cluster sur Azure
Les applications Service Fabric peuvent être exécutées sur un cluster, ou sur un ensemble de machines physiques ou virtuelles connectées au réseau. [Configurez un cluster Service Fabric s’exécutant dans Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md) avant de créer et de déployer votre application. Lorsque vous créez le cluster, choisissez une référence SKU qui prend en charge l’exécution de conteneurs (par exemple, Windows Server 2016 Datacenter avec Containers).

## <a name="containerize-the-application"></a>Conteneuriser l’application

À présent que vous disposez d’un cluster Service Fabric s’exécutant dans Azure, vous pouvez créer et déployer une application en conteneur. Pour commencer à exécuter notre application dans un conteneur, nous devons ajouter la **prise en charge Docker** au projet dans Visual Studio. Lorsque vous ajoutez la **prise en charge Docker** à l’application, deux événements se produisent. Premièrement, un _Fichier Docker_ est ajouté au projet. Ce nouveau fichier décrit la manière dont doit être générée l’image de conteneur. Dans un deuxième temps, un nouveau projet _docker-compose_ est ajouté à la solution. Le nouveau projet contient quelques fichiers docker-compose. Les fichiers docker-compose peuvent être utilisés pour décrire la manière dont le conteneur est exécuté.

En savoir plus sur l’utilisation de [Visual Studio Container Tools][link-visualstudio-container-tools].

>[!NOTE]
>Si vous utilisez pour la première fois des images de conteneurs Windows sur votre ordinateur, Docker CE doit extraire les images de base de vos conteneurs. Les images utilisées dans ce didacticiel font une taille de 14 Go. Poursuivez et exécutez la commande de terminal suivante pour extraire les images de base :
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>Ajouter la prise en charge Docker

Ouvrez le fichier [FabrikamFiber.CallCenter.sln][link-fabrikam-github] dans Visual Studio.

Cliquez avec le bouton droit sur le projet **FabrikamFiber.Web** > **Ajouter** > **Prise en charge de Docker**.

### <a name="add-support-for-sql"></a>Ajouter la prise en charge de SQL

Puisque cette application utilise SQL en tant que fournisseur de données, vous avez besoin d’un SQL Server pour exécuter l’application. Référencez une image de conteneur SQL Server dans notre fichier docker-compose.override.yml.

Dans Visual Studio, ouvrez l’**Explorateur de solutions**, cherchez **docker-compose**, puis ouvrez le fichier **docker-compose.override.yml**.

Accédez au nœud `services:`, ajoutez un nœud nommé `db:` qui définit l’entrée SQL Server pour le conteneur.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Vous pouvez utiliser l’instance SQL Server de votre choix pour le débogage local, tant qu’elle est accessible à partir de votre hôte. **localdb** ne prend cependant pas en charge la communication `container -> host`.

>[!WARNING]
>L’exécution de SQL Server dans un conteneur ne prend pas en charge les données persistantes. Lorsque le conteneur s’arrête, vos données sont effacées. N’utilisez pas cette configuration pour la production.

Accédez au nœud `fabrikamfiber.web:`, puis ajoutez un nœud enfant nommé `depends_on:`. Le service `db` (le conteneur SQL Server) peut ainsi démarrer avant notre application web (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Mettre à jour la configuration web

De retour dans le projet **FabrikamFiber.Web**, mettez à jour la chaîne de connexion dans le fichier **web.config** pour pointer vers le serveur SQL Server dans le conteneur.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Si vous souhaitez utiliser un autre SQL Server lors de la création d’une version build de votre application web, ajoutez une autre chaîne de connexion à votre fichier web.release.config.

### <a name="test-your-container"></a>Tester votre conteneur

Appuyez sur **F5** pour exécuter et déboguer l’application dans votre conteneur.

Edge ouvre la page de démarrage définie de votre application en utilisant l’adresse IP du conteneur sur le réseau NAT interne (généralement 172.x.x.x). Pour en savoir plus sur le débogage des applications dans des conteneurs à l’aide de Visual Studio 2017, consultez [cet article][link-debug-container].

![exemple de fabrikam dans un conteneur][image-web-preview]

Le conteneur est désormais prêt à être généré et empaqueté dans une application Service Fabric. Une fois l’image de conteneur intégrée sur votre ordinateur, vous pouvez la placer dans n’importe quel registre de conteneur et la transmettre à n’importe quel hôte pour l’exécuter.

## <a name="get-the-application-ready-for-the-cloud"></a>Préparer l’application pour le cloud

Pour préparer l’application en vue de son exécution dans Service Fabric dans Azure, nous devons effectuer deux étapes :

1. Exposer le port sur lequel nous souhaitons pouvoir atteindre notre application web dans le cluster Service Fabric.
2. Fournir une base de données SQL prête pour notre application.

### <a name="expose-the-port-for-the-app"></a>Exposer le port pour l’application
Le cluster Service Fabric que nous avons configuré a son port *80* ouvert par défaut dans Azure Load Balancer, qui équilibre le trafic entrant vers le cluster. Nous pouvons exposer notre conteneur sur ce port par le biais de notre fichier docker-compose.yml.

Dans Visual Studio, ouvrez **l’Explorateur de solutions**, recherchez **docker-compose** et ouvrez le fichier **docker-compose.yml**.

Modifiez le nœud `fabrikamfiber.web:`, puis ajoutez un nœud enfant nommé `ports:`.

Ajoutez une entrée chaîne `- "80:80"`. Votre fichier docker-compose.yml doit ressembler à ceci :

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Utiliser une base de données SQL de production
Lors de l’exécution dans un environnement de production, nos données doivent être persistantes dans la base de données. Comme il n’existe aucun moyen de garantir la persistance des données dans un conteneur, vous ne pouvez pas stocker les données de production de SQL Server dans un conteneur.

Nous vous recommandons d’utiliser une base de données Azure SQL Database. Pour configurer et exécuter un serveur SQL Server managé dans Azure, voir [Démarrages rapides d’Azure SQL Database][link-azure-sql].

>[!NOTE]
>N’oubliez pas de modifier les chaînes de connexion au serveur SQL dans le fichier **web.release.config** dans le projet **FabrikamFiber.Web**.
>
>Cette application échoue normalement si aucune base de données SQL n’est accessible. Vous pouvez choisir de poursuivre et de déployer l’application sans serveur SQL.

## <a name="deploy-with-visual-studio-team-services"></a>Déployer avec Visual Studio Team Services

Pour configurer le déploiement à l’aide de Visual Studio Team Services, vous devez installer [l’extension Continuous Delivery Tools pour Visual Studio 2017][link-visualstudio-cd-extension]. Cette extension facilite le déploiement dans Azure en configurant Visual Studio Team Services et vous permet de déployer votre application sur votre cluster Service Fabric.

Pour commencer, votre code doit être hébergé dans le contrôle de code source. Le reste de cette section utilise **git**.

### <a name="set-up-a-vsts-repo"></a>Configurer un référentiel VSTS
En bas à droite de Visual Studio, cliquez sur **Ajouter au contrôle de code Source** > **Git** (ou l’option de votre choix).

![appuyez sur le bouton de contrôle de code source][image-source-control]

Dans le volet _Team Explorer_, appuyez sur **Publier le dépôt Git**.

Sélectionnez le nom de votre dépôt VSTS et appuyez sur **Dépôt**.

![publier un dépôt dans VSTS][image-publish-repo]

Maintenant que votre code est synchronisé avec un dépôt de code source VSTS, vous pouvez configurer l’intégration continue et la livraison continue.

### <a name="setup-continuous-delivery"></a>Configurer une livraison continue

Dans _l’Explorateur de solutions_, cliquez avec le bouton droit sur la **solution** > **Configurer la livraison continue**.

Sélectionnez l’abonnement Azure.

Définissez **Type d’hôte** sur **Cluster Service Fabric**.

Définissez **Hôte cible** sur le cluster Service Fabric que vous avez créé dans la section précédente.

Choisissez un **Registre de conteneurs** dans lequel publier votre conteneur.

>[!TIP]
>Cliquez sur le bouton **Modifier** pour créer un registre de conteneurs.

Appuyez sur **OK**.

![configurer l’intégration continue pour service fabric][image-setup-ci]
   
   Une fois la configuration terminée, votre conteneur est déployé sur Service Fabric. Chaque fois que vous placez des mises à jour dans le dépôt, de nouvelles build et version Release sont exécutées.
   
   >[!NOTE]
   >La génération des images du conteneur prend environ 15 minutes.
   >Le premier déploiement sur le cluster Service Fabric entraîne le téléchargement des images du conteneur Windows Server Core de base. Le téléchargement prend 5 à 10 minutes supplémentaires.

Accédez à l’application Fabrikam Call Center à l’aide de l’url de votre cluster : par exemple, *http://mycluster.westeurope.cloudapp.azure.com*

Maintenant que vous avez mis en conteneur et déployé la solution de centre d’appareils Fabrikam, vous pouvez ouvrir le [portail Azure][link-azure-portal] et voir l’application en cours d’exécution dans Service Fabric. Pour tester l’application, ouvrez un navigateur web et accédez à l’URL de votre cluster Service Fabric.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un projet Docker dans Visual Studio
> * Mettre en conteneur une application existante
> * Configurer l’intégration continue avec Visual Studio et VSTS

Dans la partie suivante du didacticiel, vous apprendrez à configurer [la surveillance pour votre conteneur](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
