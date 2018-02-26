---
title: "J'utilise Mobile Services. Comment App Service peut-il m'aider ?"
description: "Découvrez les avantages qu'App Service apporte dans le cadre de vos projets Mobile Services existants."
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: 
ms.assetid: 26b68a11-8352-4f78-acd2-e4e0ec177781
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 365f00ced38a1ddc20df211121fba43efff8ea87
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="getting-started"></a>J'utilise Mobile Services. Comment App Service peut-il m'aider ?
## <a name="overview"></a>Vue d'ensemble
Votre service Mobile Service existant est sécurisé et restera pris en charge. Toutefois, des avantages fournis par la plateforme *Azure App Service* pour votre application mobile ne sont pas disponibles aujourd'hui avec Mobile Services :

* Offre plus simple, plus facile et plus économique pour les applications qui incluent à la fois des clients web et mobiles
* Nouvelles fonctionnalités d'hôte, y compris les tâches web, les enregistrements CName personnalisés et une meilleure analyse
* Intégration avec Traffic Manager
* Connectivité à vos ressources locales et réseaux privés virtuels à l'aide de VNet outre les connexions hybrides
* Surveillance, alertes et dépannage de votre application à l’aide d’AppInsights
* Plus riche éventail de ressources de calcul sous-jacentes et de tarification
* Fonctionnalités intégrées de mise à l'échelle automatique, d'équilibrage de la charge et d'analyse des performances
* Fonctionnalités intégrées intermédiaires, de sauvegarde, de restauration et de test en production

## <a name="new-hosting-features"></a>Nouvelles fonctionnalités d'hébergement
Dans *Azure App Service*, le code de backend de la fonctionnalité *Applications mobiles* s'exécute dans le même conteneur que l'application web et l'application API. Vous pouvez tirer parti de toutes les fonctionnalités dans ce conteneur, y compris de certaines qui ne figurent pas actuellement dans Mobile Services :

* Ajoutez une logique de backend qui s’exécute en permanence via les tâches web.
* Assurez-vous que votre code de backend est toujours en cours d’exécution.
* Utilisez des enregistrements CName personnalisés pour fournir des noms conviviaux et stables aux points de terminaison de votre backend mobile.
* Dimensionnez géographiquement votre application avec Traffic Manager.
* Incluez les bibliothèques et les packages que vous souhaitez.
* (Pour .NET) Utilisez des fonctionnalités d’ASP.NET, y compris MVC.
* (Pour Node.js) Utilisez toute bibliothèque JavaScript pure de l’écosystème du nœud, y compris des bibliothèques communes MVC.

## <a name="access-on-premises-data-using-vnet"></a>Accédez aux données locales à l'aide de VNet
Aujourd'hui, avec Mobile Services, vous pouvez déjà utiliser des connexions hybrides pour accéder à des ressources locales. Toutefois, dans certaines situations, une solution VPN est préférée. Avec *Azure App Service*, vous pouvez utiliser Azure VNet pour votre code de backend d'application mobile.

## <a name="use-your-favorite-backend-language"></a>Utilisez votre langage de backend favori
*Azure App Service* offre une prise en charge plus large et plus riche des plateformes ASP.NET et Node.js, ainsi qu’un accès aux derniers runtimes.

## <a name="set-up-automatic-scale"></a>Configurez la mise à l'échelle automatique
Avec Mobile Services, toutes les instances de votre code de backend s'exécutaient sur de petites machines virtuelles. *Azure App Service* vous permet de sélectionner la taille des machines virtuelles à partir d'un ensemble d'options beaucoup plus complet. Vous pouvez également augmenter rapidement la taille ou le nombre de vos machines virtuelles pour vous adapter à n’importe quelle charge cliente entrante, en fonction de diverses mesures de performances.

## <a name="be-in-the-know"></a>Restez dans la connaissance
Réagissez en temps réel aux problèmes avec la surveillance et les alertes qui vous informent automatiquement vous et votre équipe. Intégrez les fonctionnalités avancées de surveillance et d’analyse d’application d’AppInsights pour vous donner une vision des performances de votre application mobile. Avec *Azure App Service*, vous pouvez maintenant configurer des alertes basées sur diverses mesures de performances, par programmation ou via le portail Azure.

## <a name="keep-your-assets-safe"></a>Protégez vos actifs
Sauvegardez automatiquement votre backend et votre base de données. Votre code et vos données sont protégés contre une éventuelle catastrophe et peuvent être restaurées facilement, ce qui vous permet de gérer votre entreprise en toute confiance.

## <a name="ready-stage-go"></a>À vos marques, prêt, partez !
*Azure App Service* vous permet désormais de créer plusieurs environnements intermédiaires et de test privés pour vos applications mobiles. Utilisez-les pour réaliser des tests avant un déploiement. Basculez en production sans interruption de service. Les applications web sont préchargées, ce qui garantit une expérience client optimale.

Vous pouvez commencer à tirer parti d’ *App Service* pour votre application Mobile Service existante en suivant ce [didacticiel](app-service-mobile-migrating-from-mobile-services.md).
