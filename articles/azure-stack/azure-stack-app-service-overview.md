---
title: "Vue d’ensemble d’App Service : Azure Stack | Microsoft Docs"
description: "Vue d’ensemble d’App Service sur Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 1884574bcb39a1cbbd95b481adabdd06ebd499a9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="app-service-on-azure-stack-overview"></a>Vue d’ensemble d’App Service sur Azure Stack
*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure App Service sur Azure Stack est une offre PaaS (platform-as-a-service) de Microsoft Azure disponible sur Azure Stack. Le service permet à vos clients internes ou externes de créer des applications web, d’API et Azure Functions pour n’importe quelle plate-forme ou périphérique. Ils peuvent intégrer des applications locales à vos applications et automatiser leurs processus d’entreprise. Les opérateurs de cloud Azure Stack peuvent exécuter les applications client sur des machines virtuelles entièrement gérées, avec les ressources de machines virtuelles partagées ou les machines virtuelles dédiées de votre choix.

Azure App Service inclut également des fonctionnalités d’automatisation des processus d’entreprise et d’hébergement d’API cloud. En tant que service intégré unique, Azure App Service vous permet d’assembler différents éléments (sites web, API RESTful et processus d’entreprise) au sein d’une solution unique.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Pourquoi proposer Azure App Service sur Azure Stack ?

Voici quelques fonctionnalités et capacités clés d’App Service :
- **Plusieurs langages et infrastructures** : App Service offre une excellente prise en charge d’ASP.NET, Node.js, Java, PHP et Python. Vous pouvez également exécuter Windows PowerShell et d’autres scripts ou exécutables sur les machines virtuelles App Service.
- **Optimisation DevOps** : configurez l’intégration et le déploiement continus avec GitHub, local Git, ou BitBucket. Assurez la promotion des mises à jour par le biais des environnements de test et intermédiaires. Gérez vos applications dans App Service à l’aide d’Azure PowerShell ou de la CLI interplateforme.
- **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement et de débogage.

## <a name="app-types-in-app-service"></a>Types d’applications dans App Service

App Service offre plusieurs types d’application, chacun d’eux étant destiné à héberger une charge de travail spécifique :

- [Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) pour l’hébergement de sites et d’applications web.
- [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) pour l’hébergement d’API RESTful.
- Azure Functions pour l’hébergement de charges de travail sans serveur, pilotées par les événements.

Ici, le mot application fait référence aux ressources d’hébergements dédiées à l’exécution d’une charge de travail. Par exemple, vous avez certainement l’habitude de considérer qu’une « application web » constitue les ressources de calcul et le code d’application fournissant, ensemble, des fonctionnalités à un navigateur. Cependant, dans App Service, une application web constitue les ressources de calcul fournies par Azure Stack pour l’hébergement de votre code d’application.

Votre application peut être composée d’applications App Service de différents types. Par exemple, si votre application se compose d’un serveur web frontal et d’une API RESTful principale, vous pouvez :
- Déployer les deux (serveur frontal et API) sur une seule application web
- Déployer votre code frontal dans une application web et votre code principal dans une application API.

   ![](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Qu’est-ce qu’un plan App Service ?

Le fournisseur de ressources App Service utilise le même code que celui utilisé par Azure App Service. Par conséquent, il peut être utile de décrire certains concepts communs. Dans App Service, le conteneur de tarification pour les applications est appelé le plan App Service. Il représente l’ensemble des machines virtuelles dédiées utilisées pour contenir vos applications. Vous pouvez avoir plusieurs plans App Service dans un abonnement donné.

Dans Azure, il existe des Workers partagés et dédiés. Un Worker partagé prend en charge l’hébergement d’applications mutualisées à densité élevée et il n'existe qu’un seul ensemble de Workers partagés. Les serveurs dédiés sont utilisés par un seul locataire et se présentent dans trois tailles : petit, moyen et grand. Les besoins des clients locaux ne peuvent pas toujours être décrits à l’aide de ces termes. Dans App Service sur Azure Stack, les administrateurs du fournisseur de ressources peuvent définir les niveaux Worker qu'ils souhaitent rendre disponibles. Vous pouvez définir plusieurs jeux de Workers partagés ou des jeux différents de Workers dédiés selon leurs besoins d’hébergements uniques. À l’aide de ces définitions de niveau Worker, ils peuvent ensuite définir leurs propres références de tarification.

## <a name="portal-features"></a>Fonctionnalités du portail

App Service sur Azure Stack utilise la même interface utilisateur que celle utilisée par Azure App Service, ce qui est également le cas du serveur principal. Certaines fonctionnalités sont désactivées et ne sont pas fonctionnelles dans Azure Stack. Les exigences ou les services spécifiques à Azure dont ces fonctionnalités ont besoin ne sont pas encore disponibles dans Azure Stack.

## <a name="next-steps"></a>Étapes suivantes


- [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Installer le fournisseur de ressources App Service](azure-stack-app-service-deploy.md)

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md), comme le [fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md) et le [fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md).
