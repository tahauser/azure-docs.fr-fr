---
title: "Présentation d’App Service sur Linux | Microsoft Docs"
description: "Découvrez Azure App Service sur Linux."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 465e3d2c0c2b7c5d811441ede20adb9da2dbe847
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Présentation d’Azure App Service sur Linux

[Web App](../app-service-web-overview.md) est une plateforme de calcul entièrement gérée, optimisée pour l’hébergement de sites et d’applications web. Les clients peuvent utiliser App Service sur Linux pour héberger des applications web en mode natif sur Linux pour les piles d’applications prises en charge. Les sections suivantes listent les piles d’applications actuellement prises en charge.

## <a name="languages"></a>Langues

App Service sur Linux prend en charge de nombreuses images intégrées afin d’augmenter la productivité des développeurs. Si le runtime nécessaire à votre application n’est pas pris en charge dans les images intégrées, il existe des instructions sur la façon de [créer votre propre image Docker](tutorial-custom-docker-image.md) en vue de son déploiement sur Web App pour conteneurs.

| Langage | Versions prises en charge |
|---|---|
| Node.js | 4.4, 4.5, 6.2, 6.6, 6.9-6.11, 8.0, 8.1 |
| PHP | 5.6, 7.0 |
| .NET Core | 1.0, 1.1, 2.0 |
| Ruby | 2.3 |

## <a name="deployments"></a>Déploiements

* FTP
* Git local
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Environnements intermédiaires
* Intégration/livraison continue [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) et DockerHub

## <a name="console-publishing-and-debugging"></a>Console, publication et débogage

* Environnements
* Déploiements
* Console de base
* SSH

## <a name="scaling"></a>Mise à l'échelle

* Les clients peuvent faire monter ou descendre en puissance les applications web en modifiant le niveau de leur [plan App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Emplacements

Consultez le [tableau de bord Statut Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limites

Le portail Azure affiche uniquement les fonctionnalités compatibles avec Web App for Containers. Les fonctionnalités seront visibles sur le portail au fur et à mesure de leur activation.

Certaines fonctionnalités, telles que l’intégration de réseau virtuel, l’authentification Azure Active Directory/tierce, ou les extensions de site Kudu, ne sont pas encore disponibles. Dès qu’elles le seront, nous mettrons à jour notre documentation et nous annoncerons les modifications sur notre blog.

App Service sur Linux n’est pris en charge que par les plans App Service [De base et Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) et ne dispose pas d’un niveau [Gratuit ou Partagé](https://azure.microsoft.com/pricing/details/app-service/plans/). Voici d’autres restrictions importantes concernant App Service sur Linux :

* Il n’est pas possible de créer des applications Web App for Containers dans un plan App Service qui héberge déjà des applications web autres que Linux.
* Quand vous créez des applications Web App for Containers dans un groupe de ressources contenant des applications web autres que Linux, vous devez créer un plan App Service dans un groupe de ressources autre que celui du plan App Service existant.

## <a name="troubleshooting"></a>Résolution de problèmes

Lorsque votre application ne démarre pas ou que vous souhaitez vérifier la journalisation à partir de votre application, consultez les journaux Docker dans le répertoire LogFiles. Vous pouvez accéder à ce répertoire par le biais de votre site SCM ou d’un FTP.
Pour journaliser `stdout` et `stderr` à partir de votre conteneur, vous devez activer **Journalisation de conteneur Docker** sous **Journaux de diagnostic**.

![Activation de la journalisation][2]

![Affichage des journaux Docker avec Kudu][1]

Vous pouvez accéder au site SCM à partir d’**Outils avancés** dans le menu **Outils de développement**.

## <a name="next-steps"></a>Étapes suivantes

Consultez les liens ci-dessous pour vous familiariser avec App Service sur Linux. Vous pouvez poser des questions et signaler vos préoccupations sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Guide pratique pour utiliser une image Docker personnalisée pour Web App pour conteneurs](quickstart-docker-go.md)
* [Utiliser .NET Core dans Azure App Service sur Linux](quickstart-dotnetcore.md)
* [Utiliser Ruby dans Azure App Service sur Linux](quickstart-ruby.md)
* [Forum aux questions sur Azure App Service Web App for Containers](app-service-linux-faq.md)
* [Prise en charge SSH pour Azure App Service sur Linux](app-service-linux-ssh-support.md)
* [Configurer des environnements intermédiaires dans Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Déploiement continu Docker Hub avec Web App pour conteneurs](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
