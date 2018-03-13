---
title: "À propos de Mobile Apps dans Azure App Service"
description: "Découvrez les avantages qu’App Service apporte à vos applications mobiles d’entreprise."
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 082b410c9375dbdfa7f95ccd4c176e7384dad0d7
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="getting-started"></a>À propos de Mobile Apps dans Azure App Service
Azure App Service est une offre de [plateforme en tant que service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) entièrement gérée pour les développeurs professionnels. Ce service apporte un ensemble riche de fonctionnalités pour les scénarios web, mobiles et les scénarios d’intégration. 

La fonctionnalité Mobile Apps d’Azure App Service offre aux développeurs et aux intégrateurs de systèmes d’entreprise une plateforme de développement d’applications mobiles très évolutive et disponible dans le monde entier.

![Aperçu visuel des fonctionnalités Mobile Apps](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Pourquoi Mobile Apps ?
Avec la fonctionnalité Mobile Apps, vous pouvez :

* **Générer des applications natives et multiplateformes** : que vous génériez des applications natives iOS, Android et Windows ou des applications multiplateformes Xamarin ou Cordova (Phonegap), vous pouvez tirer parti d’App Service à l’aide de Kits de développement logiciel (SDK) natifs.
* **Se connecter aux systèmes de votre entreprise** : la fonctionnalité Mobile Apps vous permet d’ajouter une authentification d’entreprise en quelques minutes et de vous connecter à vos ressources locales et cloud d’entreprise.
* **Créer des applications disponibles hors connexion avec la synchronisation des données** : augmentez davantage la productivité de votre personnel mobile en créant des applications qui fonctionnent hors connexion et qui utilisent la fonctionnalité Mobile Apps pour synchroniser les données en arrière-plan quand la connexion est établie avec l’une de vos sources de données d’entreprise ou API de software as a service (SaaS).
* **Notifications push pour des millions de personnes en quelques secondes** : fidélisez vos clients en leur envoyant en temps opportun des notifications push instantanées, adaptées à leurs besoins et sur n’importe quel appareil.

## <a name="mobile-apps-features"></a>Fonctionnalités Mobile Apps
Les fonctions qui suivent sont importantes pour le développement mobile Cloud :

* **Authentification et autorisation** : prise en charge de fournisseurs d’identité, notamment Azure Active Directory pour l’authentification d’entreprise, et de fournisseurs de médias sociaux tels que Facebook, Google, Twitter et les comptes Microsoft. Mobile Apps offre un service OAuth 2.0 pour chaque fournisseur. Vous pouvez également intégrer le kit de développement logiciel du fournisseur d’identité pour la fonctionnalité spécifique du fournisseur.

    Apprenez-en davantage sur les [fonctionnalités d’authentification].

* **Accès aux données** : Mobile Apps offre une source de données OData v3 compatible avec les mobiles liée à SQL Azure Database ou à un serveur SQL Server local. Étant donné que ce service peut être développé à partir d’Entity Framework, vous pouvez l’intégrer facilement à d’autres fournisseurs de données NoSQL et SQL, notamment le [Stockage de tables Azure], MongoDB, [Azure Cosmos DB] et des fournisseurs d’API SaaS comme Office 365 et Salesforce.com.

* **Synchronisation hors connexion** : les Kits de développement logiciel (SDK) clients permettent de facilement générer des applications mobiles robustes et réactives exploitables sur des jeux de données hors connexion. Vous pouvez synchroniser automatiquement les données principales, y compris la prise en charge de la résolution de conflit.

  Apprenez-en davantage sur les [fonctionnalités de données].

* **Notifications Push** : les Kits de développement logiciel (SDK) clients s’intègrent de façon transparente aux fonctionnalités d’inscription d’Azure Notification Hubs afin de pouvoir envoyer des notifications Push à des millions d’utilisateurs simultanément.

  Apprenez-en davantage sur les [fonctionnalités de notification Push].

* **Kits de développement logiciel clients** : il existe un ensemble complet de Kits de développement logiciel clients qui concernent le développement natif ([iOS], [Android] et [Windows]), le développement multiplateforme ([Xamarin.iOS et Xamarin.Android], [Xamarin.Forms]) et le développement d’applications hybrides ([Apache Cordova]). Chaque kit de développement logiciel client est disponible avec une licence MIT et open source.

## <a name="azure-app-service-features"></a>Fonctionnalités d’Azure App Service
Les fonctionnalités suivantes de la plate-forme sont utiles aux sites de production mobile :

* **Mise à l’échelle automatique** : avec App Service, vous pouvez facilement monter en puissance ou augmenter la taille des instances pour vous adapter à n’importe quelle charge cliente entrante. Sélectionnez manuellement le nombre et la taille des machines virtuelles, ou configurez la mise à l’échelle automatique pour dimensionner votre back end d’application mobile en fonction de la charge ou d’une planification.

  Apprenez-en davantage sur la [Mise à l’échelle automatique].

* **Environnements intermédiaires** : App Service peut exécuter plusieurs versions de votre site afin de pouvoir effectuer des tests A/B, de procéder à des tests en production dans le cadre d’un plan DevOps plus étendu et d’effectuer la préproduction sur place d’un nouveau serveur principal.

  Apprenez-en davantage sur les [Environnements intermédiaires].

* **Déploiement continu** : App Service peut s’intégrer aux systèmes de gestion de _contrôle de code source_ courants, vous permettant de déployer facilement une nouvelle version de votre serveur principal.

  Apprenez-en davantage sur les [options de déploiement](../app-service/app-service-deploy-local-git.md).

* **Mise en réseau virtuelle** : App Service peut se connecter à des ressources locales à l’aide de connexions réseau virtuel, ExpressRoute Azure ou hybrides.

  Apprenez-en davantage sur les [connexions hybrides], [les réseaux virtuels] et [ExpressRoute].

* **Environnements isolés/dédiés** : pour exécuter en toute sécurité des applications Azure App Service, vous pouvez exécuter App Service dans un environnement totalement isolé et dédié. L’environnement est idéal pour des charges de travail nécessitant un accès à grande échelle, isolé ou avec réseau sécurisé.

  Apprenez-en davantage sur les [environnements App Service].

## <a name="next-steps"></a>Étapes suivantes

Pour prendre en main Mobile Apps dans Azure App Service, terminez le didacticiel de [prise en main]. Le didacticiel explique les principes fondamentaux de la production d’un back end mobile et d’un client de votre choix. Il explique également comment intégrer l’authentification, la synchronisation hors connexion et les notifications push. Vous pouvez effectuer le didacticiel plusieurs fois, une fois pour chaque application cliente.

Pour plus d’informations sur Mobile Apps, consultez notre [parcours d’apprentissage].
Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[prise en main]: app-service-mobile-ios-get-started.md
[Stockage de tables Azure]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[fonctionnalités d’authentification]: ./app-service-mobile-auth.md
[fonctionnalités de données]: ./app-service-mobile-offline-data-sync.md
[fonctionnalités de notification Push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS et Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[Mise à l’échelle automatique]: ../app-service/web-sites-scale.md
[Environnements intermédiaires]: ../app-service/web-sites-staged-publishing.md
[connexions hybrides]: ../biztalk-services/integration-hybrid-connection-overview.md
[les réseaux virtuels]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[environnements App Service]: ../app-service/environment/intro.md
[parcours d’apprentissage]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
