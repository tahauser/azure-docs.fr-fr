---
title: "Présentation des diagnostics Azure App Service | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes de votre application web avec les diagnostics App Service."
keywords: "app service, azure app service, diagnostics, prise en charge, application web, dépannage, auto-assistance"
services: app-service
documentationcenter: 
author: jen7714
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: 9526817ce7969edcd5e9c56ec153bb4e3ebaa501
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="azure-app-service-diagnostics-overview"></a>Présentation des diagnostics Azure App Service 

Quand vous exécutez une application web, vous souhaitez anticiper tout problème, des erreurs 500 à l’arrêt de votre site. Avec les diagnostics App Service, vous pouvez résoudre les problèmes de votre application web de manière intelligente et interactive sans effectuer de configuration particulière. Si vous rencontrez des problèmes avec votre application web, les diagnostics App Service vous en indiquent la nature afin que vous disposiez des informations appropriées pour les résoudre plus facilement et plus rapidement. 
 
Cette fonctionnalité est particulièrement utile quand vous rencontrez des problèmes avec votre application web au cours des dernières 24 heures, mais vous pouvez aussi analyser tous les graphes de diagnostic à tout moment. Des outils de dépannage supplémentaires et des liens vers de la documentation et des forums utiles sont indiqués dans la colonne de droite.

## <a name="open-app-service-diagnostics"></a>Ouvrir les diagnostics App Service

Pour accéder aux diagnostics App Service, accédez à votre application web App Service dans le [portail Azure](https://portal.azure.com). 

Dans le volet de navigation de gauche, cliquez sur **Diagnostiquer et résoudre les problèmes**.

![Page d’accueil](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Contrôle d’intégrité

Si vous ignorez ce qui ne va pas avec votre application web ou que vous ne savez pas où démarrer la résolution de vos problèmes, le contrôle d’intégrité constitue un bon point de départ. Le contrôle d’intégrité analyse vos applications web pour vous donner une vue d’ensemble rapide et interactive qui souligne ce qui est sain et ce qui ne va pas, vous indiquant des pistes de recherche pour mieux comprendre le problème. Son interface intelligente et interactive fournit des recommandations au fil du processus de résolution des problèmes.  

![Contrôle d’intégrité](./media/app-service-diagnostics/HealthCheckup2.png)

Si un problème appartenant à une catégorie de problèmes spécifique est détecté au cours des dernières 24 heures, vous pouvez afficher le rapport de diagnostic complet ; en consultant les diagnostics App Service, vous pouvez être invité à lire des conseils et à suivre des étapes de dépannage supplémentaires qui procurent une expérience plus interactive.

![Résolution des problèmes et étapes suivantes](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Raccourcis de vignette

Si vous savez exactement quel type d’informations vous recherchez sur la résolution des problèmes, les raccourcis de vignette vous mènent directement au rapport de diagnostic complet de la catégorie de problèmes qui vous intéresse. Par rapport au contrôle d’intégrité, les raccourcis de vignette constituent un moyen d’accès aux métriques de diagnostic plus direct, mais moins interactif.  

![Raccourcis de vignette](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Rapport de diagnostic

Que vous exécutiez un [contrôle d’intégrité](#health-checkup) pour obtenir davantage d’informations ou cliquiez sur l’un des [raccourcis de vignette](#tile-shortcuts), le rapport de diagnostic complet vous indique des métriques appropriées sous forme de graphes couvrant les dernières 24 heures. Si votre application connaît des temps d’arrêt, ces derniers sont représentés par une barre orange sous la chronologie. Vous pouvez sélectionner un des temps d’arrêt pour obtenir des observations d’analyse à son sujet et les solutions proposées. 

![Rapport de diagnostic](./media/app-service-diagnostics/DiagnosticReport5.png)

