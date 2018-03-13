---
title: "Vue d’ensemble d’Azure Policy | Microsoft Docs"
description: "Azure Policy est un service dans Azure, que vous utilisez pour créer, affecter et gérer les définitions de stratégie dans votre environnement Azure."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders; nini
ms.date: 01/17/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bb6f08e8e6252233c51c47c63ca0aec4636247dd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="what-is-azure-policy"></a>Présentation d’Azure Policy

La gouvernance informatique fait clairement la distinction entre les objectifs métier et les projets informatiques. Une bonne gouvernance informatique implique la planification de vos initiatives et la définition de priorités à un niveau stratégique. Votre société rencontre un nombre important de problèmes informatiques qui ne semblent jamais résolus ? L’implémentation de stratégies vous permet de mieux les gérer et les prévenir. C’est ici qu’Azure Policy entre en jeu.

Azure Policy est un service dans Azure, que vous utilisez pour créer, affecter et gérer des définitions de stratégie. Les définitions de stratégie appliquent différentes règles et actions sur vos ressources, qui restent donc conformes aux standards et aux contrats de niveau de service de l’entreprise. Azure Policy exécute une évaluation de vos ressources, en analysant celles qui ne sont pas conformes avec les définitions de stratégie dont vous disposez. Par exemple, vous pouvez disposer d’une stratégie pour n’autoriser que certains types de machines virtuelles. Une autre stratégie peut également exiger que toutes les ressources soient marquées. Ces stratégies sont alors évaluées lors de la création et de la mise à jour des ressources.

## <a name="how-is-it-different-from-rbac"></a>Quelle est la différence avec RBAC ?

Il existe quelques différences importantes entre la stratégie et le contrôle d’accès en fonction du rôle (RBAC). Le contrôle RBAC porte sur les actions des utilisateurs dans différentes étendues. Par exemple, le rôle de contributeur peut vous être attribué pour un groupe de ressources dans l’étendue de votre choix. Ce rôle vous permet d’apporter des modifications à ce groupe de ressources. La stratégie se focalise sur les propriétés des ressources pendant le déploiement et sur les ressources existantes. Par exemple, vous pouvez utiliser des stratégies pour contrôler les types de ressources qui peuvent être approvisionnées. Vous pouvez aussi restreindre les emplacements auxquels les ressources peuvent être approvisionnées. Contrairement à RBAC, la stratégie est, par défaut, un système explicite d'autorisation et de refus.

Pour utiliser des stratégies, vous devez vous authentifier au moyen de RBAC. Plus précisément, votre compte a besoin de :

- l’autorisation `Microsoft.Authorization/policydefinitions/write` pour définir une stratégie ;
- l’autorisation `Microsoft.Authorization/policyassignments/write` pour affecter une stratégie.

Ces autorisations ne sont pas incluses dans le rôle **Contributeur**.


## <a name="policy-definition"></a>Définition de stratégie

Chaque définition de stratégie présente des conditions dans lesquelles elle est appliquée. Si les conditions sont remplies, une action d’accompagnement a lieu.

Dans Azure Policy, nous offrons quelques stratégies intégrées qui sont disponibles par défaut. Par exemple : 

- **Nécessitent SQL Server 12.0** : cette définition de stratégie a des conditions/règles garantissant que tous les serveurs SQL Server utilisent la version 12.0. Son action consiste à refuser tous les serveurs qui ne répondent pas à ces critères.
- **Références (SKU) de compte de stockage autorisées**: cette définition de stratégie a un ensemble de conditions/règles qui déterminent si un compte de stockage qui est déployé est dans un ensemble de tailles de référence (SKU). Son action consiste à refuser tous les serveurs dont la taille ne fait pas partie de l’ensemble de tailles de références (SKU) définies.
- **Type de ressource autorisé**: cette définition de stratégie a un ensemble de conditions/règles pour spécifier les types de ressources que votre organisation peut déployer. Son action consiste à refuser toutes les ressources qui ne font pas partie de cette liste définie.
- **Emplacements autorisés** : cette stratégie vous permet de restreindre les emplacements que votre organisation peut spécifier lors du déploiement de ressources. Son action permet d’appliquer vos besoins de conformité géographique.
- **Références SKU de machine virtuelle autorisées** : cette stratégie permet de spécifier un ensemble de références SKU de machine virtuelle que votre organisation peut déployer.
- **Appliquer une balise et sa valeur par défaut** : cette stratégie applique une balise requise et sa valeur par défaut si elle n’est pas spécifiée par l’utilisateur.
- **Imposer une balise et sa valeur** : cette stratégie applique une balise requise et sa valeur à une ressource.
- **Types de ressource non autorisés** : cette stratégie permet de spécifier les types de ressource que votre organisation ne peut pas déployer.

Vous pouvez affecter l’une de ces stratégies par le biais du portail Azure, de PowerShell ou d’Azure CLI.

Pour plus d’informations sur les structures des définitions de stratégie, consultez l’article [Structure des définitions de stratégie](policy-definition.md).

## <a name="policy-assignment"></a>Affectation de rôle

Une affectation de stratégie est une définition de stratégie qui a été affectée avec une étendue spécifique. Cette étendue peut aller d’un groupe d’administration à un groupe de ressources. Le terme *étendue* désigne l’ensemble des groupes de ressources, abonnements ou groupes d’administration auxquels la définition de stratégie est affectée. Toutes les ressources enfants héritent des affectations de stratégie. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle l’est à toutes les ressources de ce groupe de ressources. Toutefois, vous pouvez exclure une sous-étendue de l’affectation de stratégie. Par exemple, dans l’étendue de l’abonnement, vous pouvez affecter une stratégie qui empêche la création de ressources réseau. Toutefois, vous excluez un groupe de ressources au sein de l’abonnement qui est destiné à l’infrastructure réseau. Vous accordez l’accès à ce groupe de ressources réseau aux utilisateurs auxquels vous faites confiance avec la création des ressources réseau.

Pour plus d’informations sur les définitions de stratégie et la définition d’affectations, consultez [Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure](assign-policy-definition.md).

## <a name="policy-parameters"></a>Paramètres de stratégie

Les paramètres de stratégie permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies que vous devez créer. Vous pouvez définir des paramètres lors de la création d’une définition de stratégie, pour la rendre plus générique. Vous pouvez ensuite réutiliser cette définition de stratégie pour différents scénarios. Pour ce faire, transmettez différentes valeurs lors de l’affectation de la définition de stratégie. Par exemple, spécifiez un ensemble d’emplacements pour un abonnement.

Les paramètres sont définis/créés lors de la création d’une définition de stratégie. Quand un paramètre est défini, un nom lui est affecté et éventuellement une valeur. Par exemple, vous pouvez définir un paramètre pour une stratégie intitulée *Emplacement*. Vous pouvez ensuite lui attribuer différentes valeurs comme *EastUS* ou *WestUS* lors de l’affectation d’une stratégie.

<!--
Next link should point to new Concept page for Parameters
-->
Pour plus d’informations sur les paramètres de stratégie, consultez [Vue d’ensemble des stratégies de ressources - Paramètres](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Définition d’initiative
Une définition d’initiative est une collection de définitions de stratégie qui sont spécialement conçues pour atteindre un objectif global particulier. Les définitions d’initiative simplifient la gestion et l’affectation des définitions de stratégie. Elles simplifient ces procédures en regroupant un ensemble de stratégies en un seul élément. Par exemple, vous pouvez créer une initiative intitulée **Activer la surveillance dans Azure Security Center**, avec comme objectif de surveiller toutes les recommandations de sécurité disponibles dans votre centre Azure Security Center.

Dans cette initiative, vous avez par exemple des définitions de stratégie comme celles-ci :

1. **Surveiller les bases de données non chiffrées dans Security Center** : pour surveiller les bases de données et les serveurs SQL Server non chiffrés.
2. **Surveiller les vulnérabilités du système d’exploitation dans Security Center** : pour surveiller les serveurs qui ne répondent pas à la base de référence configurée.
3. **Surveiller l’absence d’Endpoint Protection dans Security Center** : pour surveiller les serveurs où un agent Endpoint Protection n’est pas installé.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>Affectation d’initiative
Comme une affectation de stratégie, une affectation d’initiative est une définition d’initiative affectée à une étendue spécifique. Les affectations d’initiative réduisent la nécessité de créer plusieurs définitions d’initiative pour chaque étendue. Cette étendue peut également aller d’un groupe d’administration à un groupe de ressources.

Dans l’exemple précédent, l’initiative **Activer la surveillance dans Azure Security Center** peut être affectée à différentes étendues. Par exemple, une affectation peut être attribuée à **subscriptionA**. Une autre peut être attribuée à **subscriptionB**.

## <a name="initiative-parameters"></a>Paramètres d’initiative
Comme les paramètres de stratégie, les paramètres d’initiative permettent de simplifier la gestion en réduisant la redondance. Les paramètres d’initiative sont essentiellement la liste des paramètres utilisés par les définitions de stratégie dans l’initiative.

Par exemple, prenons un scénario où vous avez une définition d’initiative, **initiativeC**, avec deux définitions de stratégie. Chaque définition de stratégie a un paramètre défini :

| Stratégie | nom du paramètre |Type de paramètre  |Remarque |
|---|---|---|---|
| policyA | allowedLocations | array  |Ce paramètre attend une liste de chaînes pour une valeur, le type de paramètre ayant été défini comme tableau |
| policyB | allowedSingleLocation |chaîne |Ce paramètre attend un mot pour une valeur, le type de paramètre ayant été défini comme chaîne |

Dans ce scénario, quand vous définissez les paramètres d’initiative pour **initiativeC**, vous avec trois options :

1. Utiliser les paramètres des définitions de stratégie dans cette initiative : dans cet exemple, *allowedLocations* et *allowedSingleLocation* deviennent des paramètres d’initiative pour **initiativeC**.
2. Fournir des valeurs pour les paramètres des définitions de stratégie dans la définition de cette initiative. Dans cet exemple, vous pouvez fournir une liste d’emplacements au **paramètre de policyA, allowedLocations** et au **paramètre de policyB, allowedSingleLocation**.
Vous pouvez également fournir des valeurs lors de l’affectation de cette initiative.
3. Fournir une liste d’options de *valeurs* qui peuvent être utilisées lors de l’affectation de cette initiative. Lorsque vous affectez cette initiative, les paramètres hérités des définitions de stratégie dans l’initiative peuvent avoir seulement des valeurs provenant de cette liste fournie.

Par exemple, vous pouvez créer une liste d’options de valeur dans une définition d’initiative contenant les valeurs *EastUS*, *WestUS*, *CentralUS* et *WestEurope*. Si tel est le cas, vous ne pouvez pas entrer une valeur différente comme *Asie du Sud-Est* pendant l’affectation d’initiative, car elle ne fait pas partie de la liste.

## <a name="recommendations-for-managing-policies"></a>Recommandations pour la gestion des stratégies

Voici quelques conseils que nous vous recommandons de suivre lors de la création et de la gestion des définitions et des affectations de stratégie :

- Si vous créez des définitions de stratégie dans votre environnement, nous recommandons de commencer par un effet d’audit, et non pas un effet de refus, de façon à conserver un suivi de l’impact de votre définition de stratégie sur les ressources de votre environnement. Si vous avez déjà des scripts en place pour mettre à l’échelle automatiquement vos applications, la définition d’un effet de refus peut entraver ces tâches d’automatisation que vous avez déjà en place.
- Il est important de garder à l’esprit les hiérarchies de l’organisation lors de la création de définitions et d’affectations. Nous recommandons de créer les définitions à un niveau plus élevé, par exemple au niveau du groupe d’administration ou de l’abonnement, et d’affecter au niveau enfant suivant. Par exemple, si vous créez une définition de stratégie au niveau du groupe d’administration, une affectation de stratégie de cette définition peut être limitée au niveau d’un abonnement dans ce groupe d’administration.
- Nous encourageons l’utilisation d’un niveau tarifaire standard, de façon à mieux comprendre l’état de conformité de votre environnement. Pour plus d’informations sur nos modèles de tarification et sur les offres de chacun, examinons la [tarification](https://azure.microsoft.com/pricing/details/azure-policy).
- Nous recommandons de toujours utiliser des définitions d’initiative au lieu de définitions de stratégie, même si vous n’avez qu’une seule stratégie à l’esprit. Par exemple, si vous avez une définition de stratégie *policyDefA* et que vous la créez sous la définition d’initiative *initiativeDefC*, si vous décidez de créer une autre définition de stratégie ultérieurement pour *policyDefB* avec des objectifs similaires à ceux de *policyDefA*, vous pouvez simplement l’ajouter sous *initiativeDefC* et mieux les suivre de cette façon.

   N’oubliez pas qu’une fois que vous avez créé une affectation d’initiative à partir d’une définition d’initiative, toutes les nouvelles définitions de stratégie ajoutées à la définition d’initiative sont automatiquement ajoutées sous la ou les affectations d’initiative sous cette définition d’initiative. Cependant, si un nouveau paramètre est introduit dans la nouvelle définition de stratégie, vous devez mettre à jour la définition et les affectations de l’initiative en modifiant la définition ou les affectations de l’initiative.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble d’Azure Policy et des autres concepts clés que nous avons introduits, voici les étapes suivantes que nous suggérons :

- [Affecter une définition de stratégie](./assign-policy-definition.md)
- [Affecter une définition de stratégie avec Azure CLI](./assign-policy-definition-cli.md)
- [Affecter une définition de stratégie avec PowerShell](./assign-policy-definition-ps.md)
