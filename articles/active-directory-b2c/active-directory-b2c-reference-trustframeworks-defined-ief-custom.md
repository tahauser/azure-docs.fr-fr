---
title: 'Azure Active Directory B2C : informations de référence sur les infrastructures de confiance | Microsoft Docs'
description: Rubrique sur les stratégies personnalisées Azure Active Directory B2C et l’infrastructure d’expérience d’identité
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 0cd64e90ac1b6c87008b2a16ca3afa273b9c9d56
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Définir des infrastructures de confiance avec l’infrastructure d’expérience d’identité Azure AD B2C

Les stratégies personnalisées Azure Active Directory B2C (Azure AD B2C) qui utilisent l’infrastructure d’expérience d’identité fournissent un service centralisé à votre organisation. Ce service permet de réduire la complexité de la fédération des identités au sein d’une large communauté d’intérêts. La complexité est réduite à une seule relation d’approbation et à un seul échange de métadonnées.

Les stratégies personnalisées Azure AD B2C qui utilisent l’infrastructure d’expérience d’identité permettent de répondre aux questions suivantes :

- Quelles stratégies de conformité, de sécurité, de confidentialité et de protection des données doivent être appliquées ?
- Quels sont les contacts et les processus pour devenir un participant agréé ?
- Quels sont les fournisseurs d’informations d’identité agréés (également appelés « fournisseurs de revendications ») et que proposent-ils ?
- Quelles sont les parties de confiance agréées (et éventuellement, que nécessitent-elles) ?
- Quelles sont les exigences techniques d’interopérabilité réseau pour les participants ?
- Quelles sont les règles de « runtime » opérationnelles à appliquer pour échanger des informations d’identité numérique ?

Pour répondre à toutes ces questions, les stratégies personnalisées Azure AD B2C reposant sur l’infrastructure d’expérience d’identité utilisent la construction d’infrastructure de confiance. Décrivons cette construction et ce qu’elle offre.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Présentation des concepts de base de l’infrastructure de confiance et de la gestion de fédération

L’infrastructure de confiance est une spécification écrite des stratégies d’identité, de sécurité, de confidentialité et de protection des données auxquelles doivent se conformer les membres d’une communauté d’intérêt.

L’identité fédérée fournit une base pour la protection de l’identité des utilisateurs sur Internet. En déléguant la gestion des identités à des tiers, l’identité numérique unique d’un utilisateur peut être réutilisée avec plusieurs parties de confiance.  

La protection de l’identité en effet que les fournisseurs d’identité et d’attributs respectent certaines pratiques et stratégies opérationnelles, de sécurité et de confidentialité.  Si elles ne peuvent pas effectuer d’inspections directes, les parties de confiance doivent développer des relations d’approbation avec les fournisseurs d’identité et d’attributs avec lesquels elles choisissent de travailler.  

Alors que le nombre de consommateurs et de fournisseurs d’informations d’identité numérique augmente, il est difficile de continuer à gérer ces relations d’approbation par paire, ou même de procéder à un échange par paire des métadonnées techniques nécessaires pour la connectivité réseau.  Les hubs de fédération n’ont pas réussi à résoudre totalement ces problèmes.

### <a name="what-a-trust-framework-specification-defines"></a>Ce qui est défini par une infrastructure de confiance
Les infrastructures de confiance constituent l’élément central du modèle d’infrastructure de confiance Open Identity Exchange (OIX), où chaque communauté d’intérêt est régie par une spécification d’infrastructure de confiance particulière. Ce type de spécification TF définit :

- **Les métriques de sécurité et de confidentialité pour la communauté d’intérêts avec la définition de plusieurs éléments :**
    - Les niveaux de garantie offerts/exigés par les participants, par exemple un ensemble ordonné d’évaluations de confiance relatives à l’authenticité des informations d’identité numérique.
    - Les niveaux de protection offerts/exigés par les participants, par exemple un ensemble ordonné d’évaluations de confiance relatives à la protection des informations d’identité numérique gérées par les participants de la communauté d’intérêts.

- **La description des informations d’identité numérique offertes/exigées par les participants**.

- **Les stratégies techniques pour la production et la consommation des informations d’identité numérique, qui serviront à calculer les niveaux de garantie et de protection. Ces stratégies écrites incluent généralement les catégories de stratégies suivantes :**
    - Stratégies de vérification de l’identité, par exemple : *Quel est le niveau de vérification des informations d’identité d’une personne ?*
    - Stratégies de sécurité, par exemple : *Quel est le niveau de protection de la confidentialité et de l’intégrité des informations ?*
    - Stratégies de confidentialité, par exemple : *Quel contrôle un utilisateur a-t-il sur ses informations d’identification personnelle ?*
    - Stratégies de survie, par exemple : *Si un fournisseur cesse son activité, comment fonctionne la continuité et la protection des informations d’identification personnelle ?*

- **Les profils techniques pour la production et la consommation des informations d’identité numérique. Ces profils incluent :**
    - Des interfaces d’étendue pour lesquelles des informations d’identité numérique sont accessibles à un niveau de garantie spécifié.
    - Des exigences techniques d’interopérabilité réseau.

- **Les descriptions des différents rôles pouvant être attribués aux participants de la communauté, et les qualifications nécessaires pour remplir ces rôles.**

Par conséquent, une spécification TF détermine comment les informations d’identité sont échangées entre les participants de la communauté d’intérêt : les parties de confiance, les fournisseurs d’identité et d’attributs, et les vérificateurs d’attributs.

Une spécification d’infrastructure de confiance est constituée d’un ou plusieurs documents qui servent de référence pour la gouvernance de la communauté d’intérêts qui régule l’assertion et la consommation des informations d’identité numérique au sein de la communauté. Il s’agit d’un ensemble documenté de stratégies et de procédures, conçues pour établir la confiance dans les identités numériques utilisées pour les transactions en ligne entre les différents membres d’une communauté d’intérêts.  

En d’autres termes, une spécification d’infrastructure de confiance définit les règles pour la création d’un écosystème d’identités fédéré viable pour une communauté.

Il existe actuellement un large consensus sur les avantages d’une telle approche. Il ne fait aucun doute que les spécifications d’infrastructure de confiance facilitent le développement d’écosystèmes d’identité numérique avec des caractéristiques de sécurité, de garantie et de confidentialité vérifiables, ce qui signifie qu’ils peuvent être réutilisés à travers plusieurs communautés d’intérêts.

C’est pourquoi les stratégies personnalisées Azure AD B2C reposant sur l’infrastructure d’expérience d’identité utilisent la spécification comme base de leur représentation des données, afin qu’une infrastructure de confiance puisse faciliter l’interopérabilité.  

Les stratégies personnalisées Azure AD B2C qui tirent parti de l’infrastructure d’expérience d’identité représentent une spécification d’infrastructure de confiance comme un mélange de données lisibles par les humains et d’autres lisibles par un ordinateur. Certaines sections de ce modèle (en général, les sections qui sont les plus orientées vers la gouvernance) sont représentées en tant que références à une documentation publiée sur les politiques de sécurité et de confidentialité, ainsi que sur les procédures associées (le cas échéant). D’autres sections décrivent en détail les règles de métadonnées et d’exécution de configuration qui facilitent l’automatisation des opérations.

## <a name="understand-trust-framework-policies"></a>Présentation des stratégies d’infrastructure de confiance

En termes d’implémentation, la spécification d’infrastructure de confiance se compose d’un ensemble de stratégies qui permettent de contrôler totalement les comportements et les expériences d’identité.  Les stratégies personnalisées Azure AD B2C reposant sur l’infrastructure d’expérience d’identité vous permettent de créer votre propre spécification d’infrastructure de confiance via ces stratégies déclaratives qui peuvent définir et configurer :

- La ou les références de document définissant l’écosystème d’identités fédéré de la communauté associée à la spécification d’infrastructure de confiance. Il s’agit de liens vers la documentation TF. Les règles de « runtime » opérationnel (prédéfinies), ou les parcours utilisateur qui automatisent et/ou contrôlent l’échange et l’utilisation de revendications. Ces parcours utilisateur sont associés à un niveau de garantie (et de protection). Une stratégie peut donc avoir des parcours utilisateur avec différents niveaux de garantie (et de protection).

- Les fournisseurs d’identité et d’attributs, ou fournisseurs de revendications, de la communauté d’intérêt et les profils techniques qu’ils prennent en charge, ainsi que leur agrément de niveau de garantie/protection (hors bande) respectif.

- L’intégration à des vérificateurs d’attributs ou des fournisseurs de revendications.

- Les parties de confiance de la communauté (par inférence).

- Les métadonnées pour établir des communications réseau entre les participants. Ces métadonnées, ainsi que les profils techniques, sont utilisées pendant une transaction pour analyser l’interopérabilité réseau entre la partie de confiance et les autres participants de la communauté.

- Conversion de protocole, le cas échéant (par exemple SAML, OAuth2, WS-Federation et OpenID Connect).

- Les exigences d’authentification.

- L’orchestration multifacteur, le cas échéant.

- Un schéma partagé pour toutes les revendications disponibles et des mappages aux participants d’une communauté d’intérêts.

- Toutes les transformations de revendications, ainsi que la réduction possible des données dans ce contexte, pour soutenir l’échange et l’utilisation des revendications.

- La liaison et le chiffrement.

- Le stockage des revendications.

### <a name="understand-claims"></a>Présentation des revendications

> [!NOTE]
> Nous faisons collectivement référence à tous les types possibles d’informations d’identité qui peuvent être échangées en tant que « revendications » : les revendications concernant les informations d’authentification d’un utilisateur, la vérification d’identité, le périphérique de communication, l’emplacement physique, les attributs d’identification personnelle, etc.  
>
> Nous utilisons le terme « revendications » au lieu d’« d’attributs », car, dans les transactions en ligne, ces artefacts de données ne sont pas des faits directement vérifiables par la partie de confiance. Il s’agit plutôt d’assertions, ou de revendications, à propos de faits pour lesquels la partie de confiance doit développer une confiance suffisante pour accorder la transaction demandée de l’utilisateur final.  
>
> Nous utilisons aussi le terme « revendications » parce que les stratégies personnalisées Azure AD B2C qui utilisent l’infrastructure d’expérience d’identité sont conçues pour simplifier l’échange de tous types d’informations d’identité numériques de manière cohérente, que le protocole sous-jacent soit ou non défini pour l’authentification des utilisateurs ou la récupération des attributs.  De même, nous utilisons le terme « fournisseurs de revendications » pour désigner collectivement les fournisseurs d’identité, les fournisseurs d’attributs et les vérificateurs d’attributs quand nous ne voulons pas faire la distinction entre leurs fonctions respectives.   

Par conséquent, elles déterminent comment les informations d’identité sont échangées entre une partie de confiance, les fournisseurs d’identité et d’attributs, et les vérificateurs d’attributs. Elles spécifient les fournisseurs d’identité et d’attributs requis pour l’authentification d’une partie de confiance. Elles doivent être considérées comme un langage spécifique à un domaine, autrement dit un langage informatique spécialisé dans un domaine d’application spécifique avec de l’héritage, des instructions *if* et du polymorphisme.

Ces stratégies constituent la partie lisible par un ordinateur de la construction d’infrastructure de confiance dans les stratégies personnalisées d’Azure AD B2C tirant parti de l’infrastructure d’expérience d’identité. Elles comprennent tous les détails opérationnels, notamment les métadonnées et les profils techniques des fournisseurs de revendications, les définitions de schéma des revendications, les fonctions de transformation des revendications et les parcours utilisateur qui sont renseignés pour faciliter l’orchestration et l’automatisation opérationnelles.  

Elles sont supposées être des *documents dynamiques*, car il est probable que leur contenu changera au fil du temps quant aux participants actifs déclarés dans les stratégies. Il est également possible que les termes et conditions pour être participant changent.  

La configuration et la gestion de la fédération sont grandement simplifiées en épargnant aux parties de confiance la reconfiguration permanente de la connectivité et de l’approbation lors de l’arrivée ou du départ des différents fournisseurs/vérificateurs de revendications de la communauté représentée par l’ensemble de stratégies.

L’interopérabilité est un autre défi important. D’autres fournisseurs/vérificateurs de revendications doivent être intégrés, car il est peu probable que les parties de confiance prennent en charge tous les protocoles nécessaires. Les stratégies personnalisées Azure AD B2C résolvent ce problème en prenant en charge les protocoles standard et en appliquant des parcours utilisateur spécifiques pour transposer les demandes quand les parties de confiance et les fournisseurs d’attributs ne prennent pas en charge le même protocole.  

Les parcours utilisateur incluent les profils et les métadonnées des protocoles qui sont utilisés pour analyser l’interopérabilité réseau entre la partie de confiance et les autres participants. Il existe également des règles de runtime opérationnelles qui seront appliquées aux messages de demande/réponse des échanges d’informations d’identité afin de garantir la conformité avec les stratégies publiées dans le cadre de la spécification d’infrastructure de confiance. L’idée de parcours utilisateur est fondamentale pour la personnalisation de l’expérience utilisateur. Elle apporte également un éclairage sur la façon dont le système fonctionne au niveau du protocole.

Sur cette base, les portails et applications par partie de confiance peuvent, selon leur contexte, appeler des stratégies personnalisées Azure AD B2C reposant sur l’infrastructure d’expérience d’identité en passant le nom d’une stratégie spécifique afin d’obtenir le comportement et l’échange d’informations attendus sans risque ni complication.
