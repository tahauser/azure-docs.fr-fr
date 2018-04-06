---
title: Gérer des applications pour plusieurs environnements dans Azure Fabric Service | Microsoft Docs
description: Les applications Azure Fabric Service peuvent être exécutées sur des clusters comportant entre une machine et des milliers. Dans certains cas, vous devez configurer votre application différemment pour les différents environnements. Cet article explique comment définir des paramètres d’application par l’environnement.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: a3d0770d3b9c8702dbe4dac86f86030bea4090c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="manage-applications-for-multiple-environments"></a>Gestion des applications pour plusieurs environnements

Les clusters Azure Service Fabric vous permettent de créer des clusters comportant n’importe quel nombre d’ordinateurs compris entre un et plusieurs milliers. Dans la plupart des cas, vous devrez déployer votre application dans plusieurs configurations de cluster : votre cluster de développement local, un cluster de développement partagé et votre cluster de production. Tous ces clusters sont considérés comme différents environnements dans lesquels votre code doit s’exécuter. Les fichiers binaires d’application peuvent s’exécuter sans modification dans ce large éventail d’environnements, mais vous souhaiterez certainement configurer l’application différemment.

Considérez ces deux exemples simples :
  - votre service écoute sur un port défini, mais vous devez utiliser un port différent pour chaque environnement
  - vous devez fournir différentes informations d’identification de liaison pour une base de données pour chaque environnement

## <a name="specifying-configuration"></a>Spécification de la configuration

La configuration que vous fournissez peut être divisée en deux catégories :

- Configuration qui s’applique à l’exécution de vos services
  - Par exemple, le numéro de port d’un point de terminaison ou le nombre d’instances d’un service
  - Cette configuration est spécifiée dans le fichier manifeste de l’application ou du service
- Configuration qui s’applique au code de votre application
  - Par exemple, les informations de liaison d’une base de données
  - Cette configuration peut être fournie via des fichiers de configuration ou des variables d’environnement

> [!NOTE]
> Les attributs du fichier manifeste de l’application et du service ne prennent pas tous en charge les paramètres.
> Dans ce cas, vous devez substituer certaines chaînes dans le cadre de votre flux de travail de déploiement. Dans Visual Studio Team Services, vous pouvez utiliser une extension telle que Remplacer les jetons : https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens ou, dans Jenkins, vous pouvez exécuter une tâche de script pour remplacer les valeurs.
>

## <a name="specifying-parameters-during-application-creation"></a>Spécification des paramètres lors de la création d’une application

Lorsque vous créez des instances de l’application nommée dans Service Fabric, vous avez la possibilité de passer des paramètres. La façon de procéder dépend de la création de l’instance d’application.

  - Dans PowerShell, l’applet de commande [`New-ServiceFabricApplication`](https://docs.microsoft.com/en-us/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) considère les paramètres de l’application comme une table de hachage.
  - À l’aide de sfctl, la commande [`sfctl application create`](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) considère les paramètres comme une chaîne JSON. Le script install.sh utilise sfctl.
  - Visual Studio vous fournit un ensemble de fichiers de paramètres dans le dossier Paramètres du projet d’application. Ces fichiers de paramètres sont utilisés lors de la publication à partir de Visual Studio, à l’aide de Visual Studio Team Service ou Team Foundation Server. Dans Visual Studio, les fichiers de paramètres sont passés au script Deploy-FabricApplication.ps1.

## <a name="next-steps"></a>Étapes suivantes
Les articles suivants vous montrent comment utiliser certains des concepts décrits ici :

- [Guide pratique pour spécifier des variables d’environnement pour des services dans Service Fabric](service-fabric-how-to-specify-environment-variables.md)
- [Guide pratique pour spécifier le numéro de port d’un service à l’aide de paramètres dans Service Fabric](service-fabric-how-to-specify-port-number-using-parameters.md)
- [Guide pratique pour paramétrer les fichiers de configuration](service-fabric-how-to-parameterize-configuration-files.md)

- [Référence de variable d'environnement](service-fabric-environment-variables-reference.md)
