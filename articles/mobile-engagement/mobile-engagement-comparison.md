---
title: Comparaison entre Azure Mobile Engagement et des services Azure similaires
description: Comparaison entre Azure Mobile Engagement et des services Azure similaires - HockeyApp, AppInsights, Notification Hubs
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 57be3b235c5f439741155a70df7ddbe8a80906f2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Comparaison entre Azure Mobile Engagement et des services Azure similaires
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

La liste des services proposés par Microsoft Azure s’étend en continu et vous pouvez parfois vous demander en quoi Azure Mobile Engagement est différent d’un autre service dont vous venez d’entendre parler. Cet article tente de dissiper toute confusion et vous indique comment choisir Azure Mobile Engagement en fonction de votre utilisation. 

Azure Mobile Engagement est un service destiné spécifiquement **aux spécialistes du marketing numérique/directeurs marketing**, mais peut être utilisé par les **propriétaires d’applications mobiles ou les éditeurs** qui souhaitent augmenter l’utilisation, la rétention et la monétisation de leurs applications mobiles. 

*Azure Mobile Engagement est une plateforme SaaS d'engagement des utilisateurs qui fournit des informations orientées données sur l'utilisation des applications, qui permet de segmenter les utilisateurs en temps réel et d'activer les notifications Push adaptées au contexte, ainsi que la messagerie au sein de l'application.* 

Les principales caractéristiques suivantes mettent en évidence sa proposition de valeur unique :

1. **Notifications Push contextuelles et messagerie dans l’application**
   
   Il s’agit de l’avantage principal du produit : effectuer des notifications Push ciblées et personnalisées. Pour ce faire, nous collectons des données d’analyse comportementale enrichies. 
2. **Informations pilotées par les données sur l’utilisation des applications**
   
   Nous fournissons des Kits de développement logiciel (SDK) multiplateformes pour collecter les analyses comportementales sur les utilisateurs de l’application. Notez le terme « analyse comportementale » (différent du terme « analyse des performances »), car nous nous concentrons sur la façon dont les utilisateurs utilisent l’application. Même si nous recueillons des données d’analyse sur les performances de base sur les erreurs, les incidents, etc., il ne s’agit pas de l’objectif principal du produit. 
3. **Segmentation des utilisateurs en temps réel**
   
   Une fois que vous avez collecté les données d’analyse comportementale sur les utilisateurs de l’application, nous vous permettons de segmenter votre public en fonction de divers paramètres et des données collectées pour que vous puissiez lancer des campagnes push ciblées. 
4. **Logiciel en tant que service (SaaS) :**
   
   Nous avons un portail distinct du portail de gestion Azure qui est optimisé pour interagir avec les analyses comportementales enrichies sur les utilisateurs des applications et réaliser des campagnes marketing push. Le produit permet d’être opérationnel immédiatement.   

Cette différenciation étant établie, voici comment nous effectuons une comparaison par rapport à d’autres offres Azure semblables. Notez que l’article ne compare pas les niveaux de fonctionnalités, mais se base sur des scénarios permettant de définir le produit le plus adapté à vos besoins :

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) est la solution DevOps mobile de Microsoft. Avec elle, vous pouvez distribuer des versions pour les testeurs de versions béta, collecter des données liées aux pannes et obtenir des retours utilisateur. La solution est également intégrée avec Visual Studio Team Services, ce qui permet d’activer des déploiements de versions simples et d’intégrer des éléments de travail. 
   
   L’accent est mis sur les fonctionnalités DevOps et la collecte de données d’analyse sur les performances au sujet des applications mobiles. Vous pouvez intégrer à la fois HockeyApps et Mobile Engagement avec votre application. Ce cas de figure n’est pas inhabituel, même s’il existe un chevauchement dans les données collectées. Les produits ont des objectifs différents et vous aident à atteindre des objectifs différents.  
2. [Application Insights](../application-insights/app-insights-overview.md) : si votre application mobile a un côté serveur, utilisez Application Insights pour surveiller le côté serveur web de votre application et HockeyApp pour les applications mobiles côté client. 
3. [Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) fournit un service léger : cela signifie que vous n’avez pas besoin d’un Kit de développement logiciel (SDK) intégré dans l’application mobile et que vous pouvez avoir un contrôle total de votre application mobile, ce qui vous permet d’envoyer des notifications Push avec les fonctionnalités d’identification de base. C’est la solution idéale pour les propriétaires d’applications qui s’intéressent moins au ciblage qu’à l’envoi et à la communication instantanés d’informations aux utilisateurs de leur application (diffusion à un large public). 
   
   Nous mettons ici l’accent sur l’envoi de notifications très rapides avec des fonctionnalités de segmentation de base. 

Examinons quelques scénarios :

1. Tim fait partie de l’équipe marketing numérique du magasin Adventure Works qui publie des applications mobiles pour les utilisateurs. L’objectif de Tim est de s’assurer que les utilisateurs qui téléchargent les applications de l’entreprise continuent à les utiliser fréquemment. Cela augmente l’attachement à la marque des utilisateurs de l’application. En outre, cela accroît la monétisation lorsque les utilisateurs de l’application effectuent un achat à l’aide de l’application mobile. Pour cela, Tim doit envoyer des notifications ciblées et utiles aux utilisateurs de l’application, qui les encouragent à ouvrir l’application et à y revenir souvent. Mobile Engagement correspond donc parfaitement à ses attentes. 
2. Joann fait partie de l’équipe de développement des applications mobiles Adventure Works. Elle recherche un produit pour journaliser les informations sur les incidents, les exceptions et pour obtenir la télémétrie des performances à partir d’une application. HockeyApp correspond donc parfaitement à ses attentes. Joann peut intégrer HockeyApp pour les scénarios destinés aux développeurs et Azure Mobile Engagement pour Tim dans la même application, afin de tirer le meilleur parti des deux. 
3. Robin fait partie de l’équipe de développement des applications mobiles du réseau Contoso News. Elle souhaite simplement envoyer des alertes sur les actualités récentes à tous les utilisateurs sans trop de segmentation dès que l’alerte concernant l’actualité est déclenchée. Notification Hubs correspond donc parfaitement à ses attentes. 
   Dans ce scénario, à mesure que l’application mobile arrive à maturité, il est toutefois possible qu’une segmentation beaucoup plus poussée et l’obtention d’informations sur le comportement des utilisateurs de l’application soient nécessaires. À ce stade, Robin doit évaluer Azure Mobile Engagement. 

Pour résumer, l’objectif de Mobile Engagement n’est pas juste de collecter des analyses. Il ne s’agit pas uniquement d’un nouveau produit d’analyse de Microsoft. L’objectif est en effet d’envoyer des notifications Push ciblées et pour cela, nous collectons les données d’analyse comportementale ; mais le but principal est toujours d’envoyer des notifications Push qui ont un sens pour les utilisateurs de l’application et ne sont pas considérées comme du spam. 

Pour plus d’informations, consultez cette [courte vidéo](mobile-engagement-overview.md) sur Mobile Engagement. 

