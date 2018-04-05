---
title: Définir votre stratégie Mobile Engagement | Microsoft Docs
description: Découvrez comment utiliser et optimiser votre Mobile Engagement avec les analyses et les notifications Push
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7533e318-81b9-4360-aace-b7be8225985b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 494dd27acfd59eb79da13dae9bbbe0f80ee41370
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="define-your-mobile-engagement-strategy"></a>Définir votre stratégie Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

*Vous avez écrit votre application pour une raison : pour que vos utilisateurs l'utilisent !*

Nous sommes convaincus que vous avez tout mis en œuvre pour créer une application sensationnelle qui plaise aux utilisateurs. Vous avez probablement également investi un important budget marketing pour attirer les utilisateurs. Mais après le pic d’utilisateurs grisant du début, vous constatez peut-être une baisse progressive de l’utilisation de votre application. *C'est là tout l'intérêt d'Azure Mobile Engagement*: faire en sorte qu'ils continuent à utiliser votre application et que vous puissiez progressivement l'améliorer grâce aux tests et à l'apprentissage.

Notre approche visant à améliorer la fidélisation et l’utilisation est basée sur l’engagement des utilisateurs à travers les notifications Push et les messages dans l’application, mais de manière très spécifique, avec une communication et des messages personnalisés en fonction de leur comportement dans l’application. Notre objectif est de vous permettre de communiquer avec la bonne audience, au bon moment et au bon endroit.

Toutefois, pour cela, vous devez d’abord *comprendre vos utilisateurs*, puis créer des groupes en fonction de ce qu’ils font ou de leurs caractéristiques (les segments), puis créer des communications adaptées à chaque segment.

## <a name="mobile-engagement-serves-your-objectives"></a>Mobile Engagement répond à vos objectifs
*Nous avons cité la fidélisation et l’utilisation, mais dans quel but ?*

Pour créer votre stratégie Mobile Engagement, vous devez d’abord tenir compte des objectifs et des indicateurs de performance clés (KPI) de votre application.

La définition de ces objectifs et des indicateurs de performance clés vous aide à définir vos cas d’utilisation d’engagement de manière pertinente.

Vos cas d'utilisation sont une simple liste des campagnes que vous visez pour communiquer avec vos utilisateurs. Elles peuvent consister aussi bien en un simple message de bienvenue qu'en une notification très avancée déclenchée par votre système informatique. Un cas d’utilisation bien construit doit inclure au moins le trio *quoi-qui-quand* :

1. Une désignation très courte (par exemple, une « Campagne de bienvenue »)
2. **Quoi** : Un exemple de message (par exemple, « Bienvenue à bord ! N'oubliez pas de vous connecter pour bénéficier de votre premier mois gratuit ! »). Ce message n’est pas définitif, vous pourrez le modifier à tout moment, mais il constitue généralement un bon point de départ pour réfléchir à ce que vous voulez dire
3. **Qui**: le segment qui reçoit ce message (par exemple, « Tous les utilisateurs qui ont lancé l’application pour la première fois il y a 3 jours, ont visité la page de connexion, mais ne se sont pas connectés »)
   * Oui, vous pouvez le faire facilement avec Azure Mobile Engagement :)
   * Là encore, le choix n’est pas définitif, car vous pouvez définir vos segments à tout moment, mais il est important de choisir assez tôt vos critères de segmentation pour collecter les données appropriées
4. **Quand**: la planification de votre campagne. Il peut s'agir d'une date donnée ou du moment qui suit une action spécifique, basé sur un déclencheur. Mobile Engagement offre de nombreuses possibilités de bien planifier votre communication

Une fois les cas d'utilisation et les segments définis, vous recevez des conseils pour définir les données à collecter dans l'application. C’est le rôle du *« plan de balises »*. Un plan de balises permet de vous assurer que la collecte de données est spécifiée pour les développeurs. De cette façon, ces derniers peuvent incorporer Mobile Engagement avec la configuration appropriée, qui vous permettra d'utiliser vos campagnes avec les bonnes données. N'oubliez pas d'effectuer des tests pour vérifier que l'intégration est correcte et qu'elle collecte les informations dont vous avez besoin.

En fonction de l’intégration et une fois que les applications sont publiées, vous pouvez, en tant que spécialiste marketing, surveiller vos analyses en temps réel, segmenter vos audiences, puis commencer à envoyer des notifications Push intelligentes et ciblées pour communiquer avec les utilisateurs finaux dans ou en dehors de l’application.

### <a name="use-cases-to-get-started"></a>Cas d'utilisation pour la prise en main
1. Stratégie de bienvenue : créez plusieurs campagnes de notification push basées sur le comportement de l'utilisateur final au lancement de l'application pour relancer la communication à J+2/5/10/15 après la première session et augmenter la fidélisation lors de la première exécution.
2. Faites la promotion d’un nouveau contenu (fonctionnalité, article/vidéo ou produit) en fonction du comportement de l’utilisateur final pour envoyer les informations uniquement aux utilisateurs finaux les plus susceptibles de s’engager.
3. Évaluez l’application : ciblez moins d’1 % de votre base d’utilisateurs susceptibles d’accorder 5 étoiles à l’application dans la boutique d’applications.
4. Encouragez les abonnements : faites la promotion d’un contenu intéressant pour les utilisateurs finaux qui ne l’ont pas encore vu, pour augmenter le nombre d’abonnements.
5. Didacticiel : n'imposez plus de didacticiel à tous les utilisateurs. Il est plus judicieux de créer des didacticiels intéressants dans l’application, puis de les déclencher par le biais de messages dans l’application uniquement si l’utilisateur semble ne pas utiliser une fonctionnalité ou rencontrer des difficultés pour l’utiliser.

## <a name="why-do-you-need-analytics-to-engage"></a>Pourquoi avez-vous besoin des analyses pour favoriser l’engagement ?
Comme vous le réalisez peut-être à ce stade, la diffusion de notifications push ne suffit pas. Le concept de base de Mobile Engagement est d’aider les spécialistes marketing et les développeurs à communiquer avec le bon utilisateur final, au bon moment et au bon endroit. Pour connaître ces trois paramètres essentiels, il est primordial d'analyser votre application et d'utiliser le résultat de ces analyses pour segmenter votre audience. Le bénéfice est encore plus grand quand des segments de comportements viennent compléter les données d'une autre base de données ou CRM, ou d'un canal croisé. Mobile Engagement permet de récupérer des données de n'importe quelle source et les utilise pour cibler la bonne audience.

Pour être le plus contextuel possible quand vous communiquez avec votre audience, vous devez connaître le comportement de vos utilisateurs finaux, pour déterminer leur situation en temps réel. La collecte de données permet aux spécialistes marketing de se concentrer vraiment sur l’essentiel pour utiliser les cas d’utilisation et atteindre leurs objectifs de stratégie d’engagement mobile. Pour atteindre les objectifs définis plus tôt, la meilleure pratique ne consiste pas à utiliser toutes les analyses, mais uniquement celles qui vous permettent de vous concentrer sur ce que vous voulez apprendre et sur vos cas pratiques. C’est la bonne méthode pour commencer : essayez, testez et apprenez à utiliser la solution, à envoyer des notifications Push intelligentes et à augmenter la fidélisation pour que l’application devienne un succès.

> [!NOTE]
> N’oubliez pas : trop de données tuent les données !
> 
> 

### <a name="use-cases-and-best-practices"></a>Cas d’utilisation et meilleures pratiques
Dans les sections qui suivent, nous allons maintenant brièvement examiner certains cas d’utilisation clés rapportés par nos clients pour vous aider à faire vos premiers pas.

#### <a name="media"></a>Médias
Collectez des informations sur le type de contenu consommé par l’utilisateur final, puis segmentez l’audience en fonction de ce comportement pour cibler des types de contenu spécifique seulement sur l’audience susceptible de le consommer. De cette façon, vous évitez d'envoyer des courriers indésirables à toute une base d'utilisateurs et vous assurez une meilleure fidélisation.

#### <a name="m-commerce"></a>M-commerce
Collectez des informations sur les catégories de produit les plus visitées dans l'application et ciblez l'audience à laquelle vous pouvez proposer une réduction ou un nouveau produit que l'utilisateur final sera plus susceptible d'acheter dans cette catégorie. Cherchez à augmenter votre chiffre d'affaires. Là encore, l'objectif n'est pas d'envoyer du courrier indésirable !

#### <a name="gaming"></a>Jeux
Collectez des informations sur le niveau de jeu d’un utilisateur final et le temps passé au cours d’une période donnée pour cibler l’audience qui pourrait être bloquée et serait susceptible d’atteindre un niveau supérieur avec une offre de bonus.

Communiquez sur des événements spécifiques avec une offre incitative auprès des utilisateurs qui n’ont pas joué depuis un certain temps pour essayer de les faire revenir

#### <a name="retail"></a>Vente au détail
Collectez des informations sur les produits ou les marques qu’une audience est susceptible de consommer d’après ses favoris ou son comportement, et dirigez cette audience vers la boutique d’applications pour augmenter le chiffre d’affaires des achats.

#### <a name="banking"></a>Opérations bancaires
Collectez des informations pour savoir si les utilisateurs finaux ont créé un compte au premier lancement de l’application. Cherchez à déployer une stratégie de bienvenue avec des notifications Push ciblées et augmentez le nombre d’abonnements de compte.

### <a name="how-to-create-a-great-tag-plan"></a>Comment créer un plan de balises efficace ?
Un plan de balises doit s'apparenter à une description du parcours de l'utilisateur ou à une sorte de flux de travail de l'application, et fournir toutes les balises nécessaires (données) qui doivent être collectées pour avoir suffisamment d'analyses afin de comprendre le comportement de l'utilisateur et de segmenter correctement la base d'utilisateurs. Il ne s'agit pas d'un processus technique. Les spécialistes marketing peuvent donc spécifier les données à collecter en fonction de leur stratégie Mobile Engagement.

Il faut au moins baliser tous les écrans (ou *activités* dans Mobile Engagement) d’une application. Cela permet de déterminer le chemin d’accès utilisateur.

Une activité peut contenir des *événements* qui collectent des informations d’action, par exemple, un clic sur un bouton. Cela permet de collecter des informations sur l’interaction dans l’application. Les spécialistes marketing peuvent donc identifier les écrans visités par les utilisateurs et savoir ce qu’ils font.

`Jobs` sont des actions avec une durée. Elles sont très utiles aux spécialistes marketing qui souhaitent connaître le temps nécessaire à un utilisateur pour créer un compte ou se connecter, par exemple. Elles peuvent aussi servir aux développeurs pour contrôler le temps nécessaire pour appeler un service web.

`Errors` peuvent également être analysées pour savoir si les utilisateurs rencontrent des problèmes dans votre application. Par exemple, s’ils rencontrent souvent des problèmes de connexion.

Tous ces types de données peuvent être agrémentés de paramètres (*extra-information* dans Mobile Engagement), qui permettent de collecter des données dynamiques à partir de l’application. Il s’agit d’un élément important pour obtenir une segmentation plus fine. Par exemple, les spécialistes marketing peuvent segmenter les utilisateurs en fonction du type de contenu qu’ils ont consommé. Le type de contenu représente les informations dynamiques d’une activité ou d’un événement.

*Les informations de l’application* sont les données qui vous confirment l’état de l’application ou de l’utilisateur en temps réel. Elle permet également de catégoriser une base d’audience de base et de la cibler rapidement. Par exemple, il peut utiliser un état true/false indiquant si l’utilisateur se connecte ou non, ou la date d’expiration de son abonnement.

#### <a name="example-of-tags"></a>Exemple de balises
*Cas d’utilisation : segmenter le comportement des audiences pour cibler le bon utilisateur final avec le bon contenu de notification Push*

1. Envoyez des notifications Push pour promouvoir une catégorie de produit : collectez les données de comportement pour segmenter l’audience en fonction de la catégorie de produit visitée x fois dans une période donnée ou d’un élément spécifique ajouté au panier d’achat. Les données collectées permettent de segmenter les audiences, puis d’envoyer une notification Push à la bonne audience.
2. Évaluez l’application : collectez des données en fonction du contenu partagé par l’audience sur un réseau social. Vise à segmenter l’audience en déterminant les *ambassadeurs* de votre application. Avec une notification Push dans l’application, les ambassadeurs sont la meilleure audience de votre application à demander d’évaluer votre application à 5 étoiles dans le magasin.
   
   ![][1]

*Cas d’utilisation : données déclaratives*

1. Segmentez les alertes de contenu : collectez des données déclaratives pour segmenter l'audience en fonction des préférences. Ainsi, vous pouvez envoyer une notification push sur un sujet particulier qui intéresse vraiment une audience spécifique.
2. Segmentez l'audience en fonction de l'état de connexion. Collectez des données pour savoir si un utilisateur est connecté ou s'il a créé un compte. Ces données ciblent les utilisateurs finaux qui ne sont pas encore connectés et d’envoyer une notification Push pour inciter l’utilisateur final à effectuer la conversion.
   ![][2]

### <a name="next-steps"></a>Étapes suivantes
* Consultez la page [Concepts de Mobile Engagement] pour en savoir plus sur les concepts Mobile Engagement de base.
* Consultez l’article [Création d’un compte Azure Mobile Engagement](mobile-engagement-create.md) pour apprendre à créer une collection d’applications Mobile Engagement dans Azure et commencer à gérer vos applications à l’aide du portail Mobile Engagement.
* Consultez les [Meilleures pratiques](mobile-engagement-getting-started-best-practices.md) pour une étude approfondie.
* Consultez le [Scénario d’application de jeu](mobile-engagement-gaming-scenario.md) pour en savoir plus sur l’implémentation de Mobile Engagement avec un exemple d’application de jeu. 
* Consultez le [Scénario d’application multimédia](mobile-engagement-media-scenario.md) pour en savoir plus sur l’implémentation de Mobile Engagement avec un exemple d’application multimédia. 
* Consultez la page [Didacticiels] pour en savoir plus sur l'implémentation.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Concepts de Mobile Engagement]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Didacticiels]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

