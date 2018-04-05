---
title: Mise en œuvre d’Azure Mobile Engagement avec une application multimédia
description: Scénario d’application multimédia pour mettre en œuvre Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8e5d4585b47a4ca26b81b7168aa499f25722415a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-media-app"></a>Mise en œuvre de Mobile Engagement avec une application multimédia
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

## <a name="overview"></a>Vue d'ensemble
John est chef de projet mobile pour une grande société multimédia. Il a récemment lancé une nouvelle application qui enregistre un grand nombre de téléchargements. Bien qu’il ait atteint ses objectifs en matière de téléchargements, son retour sur investissement (ROI) par utilisateur n’est pas encore à la hauteur de ses espérances. 

John a déjà identifié les raisons pour lesquelles le retour sur investissement est si faible. Les utilisateurs arrêtent d’utiliser son application au bout de seulement 2 semaines, et la plupart d’entre eux ne revient jamais. Il souhaite augmenter le taux de rétention de son application.

Après quelques analyses, il a compris que lorsqu’il sollicite ses utilisateurs avec des notifications Push, ils ont tendance à continuer d’utiliser son application. De plus, les utilisateurs devenus inactifs reviennent souvent sur l’application en fonction des notifications qu’il leur envoie. John décide alors d’investir dans un programme d’engagement utilisant le ciblage avec notifications Push pour son application.

John a récemment lu [Azure Mobile Engagement - Guide de prise en main et meilleures pratiques](mobile-engagement-getting-started-best-practices.md) et a décidé de mettre en œuvre les recommandations préconisées dans le guide.

## <a name="objectives-and-kpis"></a>Objectifs et indicateurs clés de performance
Rencontre entre les différentes parties prenantes de l’application de John. Les recettes sont générées par les clics sur les annonces au fur et à mesure que les utilisateurs utilisent les médias En augmentant le contenu consommé par utilisateur, John accroît son chiffre d’affaires. Tous sont d’accord sur un objectif principal : augmenter les ventes de publicités de 25 %. Ils créent des indicateurs clés de performance (KPI) pour mesurer et atteindre cet objectif

* Nombre de clics sur les annonces par utilisateur
* Nombre de pages d’articles visitées (par utilisateur/par session/par semaine/par mois...)
* Quelles sont les catégories favorites

À la suite de la réunion de John avec les différentes parties prenantes, des indicateurs clés de performance sont définis. Il applique la partie 1 de [Azure Mobile Engagement - Guide de prise en main et meilleures pratiques](mobile-engagement-getting-started-best-practices.md) 

Ensuite, il crée les indicateurs clés de performance d’engagement suivants pour s’assurer que les objectifs seront atteints :

* Suivre le taux de rétention sur les périodes suivantes : quotidien, hebdomadaire, bihebdomadaire et mensuel.
* Comptes d’utilisateurs actifs
* Évaluation de l’application dans les app store

Selon les recommandations de l’équipe informatique, les indicateurs clés de performance techniques suivants ont été ajoutés pour répondre aux questions suivantes :

* Quel est mon parcours utilisateur (pages visitées, temps passé dessus par l’utilisateur) ?
* Quel est le nombre de pannes ou de bogues par session ?
* Quelles sont les versions de système d’exploitation exécutées par les utilisateurs ?
* Quelle est la taille moyenne de l’écran des utilisateurs ?
* De quels types de connexions Internet mes utilisateurs doivent-ils disposer ?

Pour chaque indicateur clé de performance, il classe les données requises et les enregistre à l’emplacement approprié de son manuel.

## <a name="engagement-program-and-integration"></a>Programme d’engagement et intégration
Maintenant que John a terminé de définir ses indicateurs clés de performance, il entame la phase de stratégie d’engagement en définissant 4 programmes d’engagement et les objectifs associés : ![][1]

John approfondit ensuite en détaillant des notifications Push pour chaque programme. Les notifications Push sont définies par cinq éléments :

1. Objectif : quel est l’objectif de la notification ?
2. Comment l’objectif sera-t-il atteint ?
3. Cible : qui recevra la notification ?
4. Contenu : quels sont la teneur et le format de la notification (dans ou en dehors de l’application) ?
5. Quand : quel est le meilleur moment pour envoyer cette notification Push ?
   
    ![][2]

Pour plus d’informations, reportez-vous aux [Manuels](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

En accord avec la partie 2 du livre blanc, John utilise la section cible pour déterminer les données qu’il doit recueillir et rédige un plan de balises avec l’équipe informatique pour mettre en œuvre la solution. Après 1 semaine de mise en œuvre et de tests d’acceptation des utilisateurs, John peut enfin lancer ses programmes.

## <a name="program-results"></a>Résultats du programme
4 mois plus tard, John examine les performances des programmes. Le programme de bienvenue et le programme hebdomadaire atteignent leurs objectifs. Le nombre d’utilisateurs avec une seule session diminue, plusieurs fonctionnalités de l’application sont utilisées, et le nombre de connexions par semaine a doublé.

Le **Programme inactif** aide John à comprendre les tendances des utilisateurs. 15 % des utilisateurs inactifs reviennent sur l’application. Toutefois, la plupart d’entre eux ne restent pas actifs plus de 1 mois. John prévoit une optimisation de cette séquence avec des notifications supplémentaires et le développement du choix de contenu.

Le **Programme de découverte** ne fonctionne pas correctement. Il permet d’augmenter les ventes croisées, mais pas suffisamment pour atteindre les objectifs. John se rend compte qu’il n’a pas suffisamment de données pour faire un ciblage pertinent et propose du contenu approprié. Il arrête ce programme et se concentre sur l’envoi de « notifications Push éditoriales » avec Azure Mobile Engagement. Ses rédacteurs disposent déjà d’une solution de gestion de contenus pour envoyer des notifications Push et ne veulent pas en changer.

John décide d’utiliser l’API Reach qui est une API REST HTTP qui permet de gérer des campagnes Reach sans avoir à utiliser l’interface Web AZME. Grâce à cette approche, John peut désormais collecter les données dont il a besoin et permettre à ses rédacteurs de continuer à utiliser leur solution CMS.

Pour que cette fonctionnalité fonctionne correctement, John demande à l’équipe informatique d’être vigilante sur les points suivants :

1. **Systèmes d’exploitation** : chacun d’eux à ses propres règles pour gérer des notifications Push. John décide donc de répertorier tous les cas et vérifie si les API les prennent en charge.
   Par exemple : le système Push sous Android permet l’affichage d’images de grande taille, ce qui n’est pas le cas avec iOS.
2. **Période**: John veut une API qui définit un délai et une date de fin de campagne. Il souhaite éviter aux utilisateurs tout envoi de notification intempestive.
3. **Catégories**: l’équipe Marketing prépare un modèle pour chaque type d’alerte. John demande à l’équipe informatique de définir des catégories dans l’API.

Après quelques essais, John est satisfait. Grâce à cette API, les journalistes peuvent toujours envoyer des notifications Push avec leurs CMS et Azure Mobile Engagement se charge de recueillir toutes les données liées aux comportements pour eux

Après ces 4 premiers mois, les résultats concluent à de bonnes performances générales. John et son équipe sont confiants ; le ROI par utilisateur a augmenté de 15 % et les ventes mobiles représentent 17,5 % du total des ventes, soit une augmentation de 7,5 % en seulement quatre mois.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
