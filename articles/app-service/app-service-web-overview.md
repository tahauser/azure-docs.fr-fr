---
title: Présentation de Web Apps | Microsoft Docs
description: Découvrez comment Azure App Service vous aide à développer et héberger des applications web.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: e789d3c6e10399491eb055b850a590ae10aacd11
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="web-apps-overview"></a>Vue d'ensemble de Web Apps

*Azure App Service Web Apps* (ou simplement Web Apps) est un service pour l’hébergement d’applications web, d’API REST et de backends mobiles. Vous pouvez développer dans votre langage préféré, par exemple .NET, .NET Core, Java, Ruby, Node.js, PHP ou Python. Vous pouvez exécuter et mettre à l’échelle des applications en toute simplicité sur des machines virtuelles Windows ou Linux (consultez [App Service sur Linux](containers/app-service-linux-intro.md)). 

Web Apps non seulement ajoute la puissance de Microsoft Azure à votre application, par exemple la sécurité, l’équilibrage de charge, la mise à l’échelle automatique et la gestion automatisée. Vous pouvez également bénéficier de ses fonctionnalités DevOps, notamment le déploiement continu à partir de VSTS, GitHub, Docker Hub et d’autres sources, la gestion des packages, les environnements intermédiaires et les certificats SSL. 

Avec App Service, vous payez pour les ressources de calcul Azure que vous utilisez. Les ressources de calcul que vous utilisez sont déterminées par le _plan App Service_ sur lequel vous exécutez Web Apps. Pour plus d’informations, consultez [Plans App Service dans Azure Web Apps](azure-web-sites-web-hosting-plans-in-depth-overview.md).

## <a name="why-use-web-apps"></a>Pourquoi utiliser Web Apps ?

Voici quelques-unes des principales fonctionnalités d’App Service Web Apps :

* **Plusieurs langages et infrastructures**: Web Apps offre une prise en charge de première classe pour ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Vous pouvez également exécuter [PowerShell et d’autres scripts ou exécutables](web-sites-create-web-jobs.md) comme services en arrière-plan.
* **Optimisation DevOps** : configurez [l’intégration et le déploiement continus](app-service-continuous-deployment.md) avec Visual Studio Team Services, GitHub, BitBucket, Docker Hub ou Azure Container Registry. Assurez la promotion des mises à jour par le biais des [environnements de test et intermédiaires](web-sites-staged-publishing.md). Gérez vos applications dans Web Apps à l’aide d’[Azure PowerShell](/powershell/azureps-cmdlets-docs) ou de l’[interface CLI interplateforme](/cli/azure/install-azure-cli).
* **Mise à l’échelle globale avec une haute disponibilité** : effectuez des [montées en puissance](web-sites-scale.md) ou [augmentez la taille des instances](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellement ou automatiquement. Hébergez vos applications n’importe où dans l’infrastructure mondiale des centres de données de Microsoft, et bénéficiez des garanties du [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) App Service en matière de haute disponibilité.
* **Connexion aux plateformes SaaS et données locales** : choisissez parmi plus de 50 [connecteurs](../connectors/apis-list.md) pour des systèmes d’entreprise tels que SAP, des services SaaS tels que Salesforce et des services Internet tels que Facebook. Accédez aux données locales à l’aide de [connexions hybrides](../biztalk-services/integration-hybrid-connection-overview.md) et de [réseaux virtuels Azure](web-sites-integrate-with-vnet.md).
* **Sécurité et conformité** : App Service est [conforme aux normes ISO, SOC et PCI](https://www.microsoft.com/TrustCenter/). Authentifiez les utilisateurs avec [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) ou avec une connexion sociale ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) et [ Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)). Créez des [restrictions par adresse IP](app-service-ip-restrictions.md) et [gérez les identités de service](app-service-managed-service-identity.md).
* **Modèles d’application** : faites votre choix parmi une liste complète de modèles d’application dans [Place de marché Azure](https://azure.microsoft.com/marketplace/), tels que WordPress, Joomla et Drupal.
* **Intégration Visual Studio** : les outils dédiés de Visual Studio rationalisent le travail de création, de déploiement et de débogage.
* **API et fonctionnalités mobiles** : Web Apps fournit une prise en charge CORS clé en main pour les scénarios API RESTful et simplifie les scénarios d’application mobile grâce à l’authentification, la synchronisation des données hors connexion, des notifications push et bien plus encore.
* **Code sans serveur** : exécutez un extrait de code ou un script à la demande sans avoir à configurer ou gérer explicitement l’infrastructure, et ne payez que pour le temps de calcul que votre code utilise réellement (voir [Azure Functions](/azure/azure-functions/)).

En plus des applications web dans App Service, Azure offre d’autres services qui peuvent être utilisés pour l’hébergement de sites et d’applications web. Pour la plupart des scénarios, Web Apps est le meilleur choix.  Pour l’architecture microservice, utilisez [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Si vous avez besoin de contrôler davantage les machines virtuelles sur lesquelles votre code s’exécute, utilisez plutôt [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Pour plus d’informations sur le choix entre ces services Azure, consultez [Comparaison entre Azure App Service, Virtual Machines, Service Fabric et Cloud Services](choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Étapes suivantes

Créez votre première application web.

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Node.JS](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
