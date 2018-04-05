---
title: Mise en œuvre d’Azure Mobile Engagement avec une application de jeux
description: Scénario d’application de jeux pour mettre en œuvre Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 96e827b57e804cc91798859bc906e49046decede
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>Mise en œuvre de Mobile Engagement avec une application de jeu
## <a name="overview"></a>Vue d'ensemble
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Une jeune entreprise spécialisée dans les jeux a lancé un nouveau jeu de rôle/de stratégie sur le thème de la pêche. Ce jeu est opérationnel depuis 6 mois. Il connaît un énorme succès et a été téléchargé des millions de fois. De plus, la rétention est très élevée par rapport à d’autres applications de jeu. Lors de la réunion d’examen trimestrielle, les différents intervenants estiment qu’ils doivent augmenter les revenus moyens par utilisateur (revenu moyen par abonné). Des packages au sein du jeu sont disponibles sous forme d’offres spéciales. Ces packages de jeu permettent aux utilisateurs de mettre à niveau l’apparence et les performances de leurs lignes de pêche et des appâts et des articles au sein du jeu. Toutefois, les ventes de package sont très faibles. Par conséquent, il a été décidé dans un premier temps d’analyser l’expérience client à l’aide d’un outil d’analyse, puis de développer un programme d’engagement afin d’augmenter les ventes au moyen d’une segmentation avancée.

En s’appuyant sur [Azure Mobile Engagement - Guide de prise en main et meilleures pratiques](mobile-engagement-getting-started-best-practices.md) , ils ont élaboré une stratégie d’engagement.

## <a name="objectives-and-kpis"></a>Objectifs et indicateurs clés de performance
Les différents intervenants du jeu se réunissent. Tous les intervenants sont d’accord sur un objectif principal : augmenter les ventes de packages premium de 15 %. Ils créent des indicateurs clés de performance (KPI) pour mesurer et atteindre cet objectif.

* À quel niveau de la partie ces packages sont-ils achetés ?
* Quel est le chiffre d’affaires par utilisateur, par session, par semaine et par mois ?
* Quels sont les types d’achats préférés ?

La première partie du [Guide de prise en main](mobile-engagement-getting-started-best-practices.md) explique comment définir des objectifs et des indicateurs de performance clés. 

Une fois les indicateurs de performance clés d’entreprise définis, le responsable de produit mobile crée des indicateurs clés de performance clés pour déterminer les nouvelles tendances et rétention utilisateur.

* Contrôler la rétention et l’utilisation pendant les intervalles suivants : quotidien, tous les 2 jours, hebdomadaire, mensuel et trimestriel
* Nombre d’utilisateurs actifs
* Évaluation de l’application dans le Windows Store

Selon les recommandations de l’équipe informatique, les indicateurs clés de performance techniques suivants ont été ajoutés pour répondre aux questions suivantes :

* Quel est mon parcours utilisateur (page visitée, temps passé dessus par l’utilisateur) ?
* Quel est le nombre de pannes ou de bogues par session ?
* Quelles sont les versions de système d’exploitation exécutées par mes utilisateurs ?
* Quelle est la taille moyenne de l’écran de mes utilisateurs ?
* De quels types de connectivité Internet mes utilisateurs doivent-ils disposer ?

Pour chaque indicateur de performance, le responsable de produit mobile spécifie les données nécessaires et l’endroit du manuel où elles se trouvent.

## <a name="engagement-program-and-integration"></a>Programme d’engagement et intégration
Avant de réaliser un programme d’engagement avancé, le responsable de projet mobile en charge doit bien connaître la manière et les moments où les produits sont consommés par les utilisateurs.

Après 3 mois, le responsable de projet mobile a recueilli suffisamment de données pour améliorer ses ventes par notification Push au sein de l’application. Il a notamment appris que :

* Le premier achat a généralement lieu au niveau 14. Dans 90 % des cas, l’achat concerne des armes légendaires de 3 $.
* Dans 80 % des cas, les utilisateurs qui ont effectué un achat continuent avec le produit et effectuent d’autres achats.
* Les utilisateurs qui ont réussi le niveau 20 commencent à dépenser plus de 10 $/semaine.
* Les utilisateurs ont tendance à acheter des packages premium aux niveaux 16, 24 et 32.

Grâce à cette analyse, le responsable de projet mobile décide de créer des séquences de notification Push spécifiques pour augmenter les ventes d’application. Il crée trois séquences Push qu’il nomme respectivement Bienvenue dans le programme, Programme commercial et Programme inactif. Pour plus d’informations, reportez-vous aux [Manuels](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
