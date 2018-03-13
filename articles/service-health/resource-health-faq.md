---
title: FAQ Azure Resource Health | Microsoft Docs
description: "Vue d’ensemble d’Azure Resource Health"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/05/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 692d03ad80989a969a73b15bb9042a01c286d1ec
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="azure-resource-health-faq"></a>FAQ Azure Resource Health
Découvrez les réponses aux questions courantes sur Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Qu’est-ce qu’Azure Resource Health ?
Resource Health vous permet d’établir des diagnostics et d’obtenir de l’aide lorsqu’un problème touchant Azure a des répercussions sur vos ressources. Il vous informe de l’intégrité (actuelle et passée) de vos ressources et vous aide à atténuer les problèmes. Resource Health propose un support technique dès lors que vous êtes confronté à des problèmes de service Azure et que vous avez besoin d’aide.  

## <a name="what-is-the-resource-health-intended-for"></a>À quoi sert Resource Health ?
Une fois qu’un problème lié à une ressource a été détecté, Resource Health peut vous aider à en diagnostiquer la cause première. Il fournit de l’aide pour résoudre le problème et un support technique si vous avez besoin d’aide supplémentaire concernant des problèmes liés aux services Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Quels contrôles d’intégrité sont effectués par Resource Health ?
Resource Health effectue plusieurs vérifications selon le [type de ressource](resource-health-checks-resource-types.md). Ces contrôles sont conçus pour résoudre trois types de problèmes : 
- Événements non planifiés, par exemple le redémarrage d’un hôte inattendu
- Événements planifiés, telles que les mises à jour planifiées du système d’exploitation hôte
- Événements déclenchés par des actions de l’utilisateur, par exemple le redémarrage d’une machine virtuelle

## <a name="what-does-each-of-the-health-status-mean"></a>Que signifie chaque état Resource Health ?
Il existe trois états différents :
- Disponible : aucun problème connu sur la plateforme Azure susceptible d’impacter cette ressource
- Non disponible : Resource Health a détecté des problèmes qui impactent la ressource
- Inconnu : Resource Health ne peut pas déterminer l’intégrité d’une ressource, car il ne reçoit plus d’informations la concernant. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Que signifie l’état inconnu ? Y a-t-il un problème avec ma ressource ?
L’état d’intégrité est défini sur Inconnu lorsque Resource Health ne reçoit plus d’informations concernant une ressource spécifique. Si cet état n’est pas une indication définitive de l’état de la ressource, cela peut indiquer un problème Azure si vous rencontrez des difficultés.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Comment puis-je obtenir de l’aide pour une ressource qui n’est pas disponible ?
Vous pouvez envoyer une demande de support à partir du panneau Resource Health. Vous n’avez pas besoin d’un contrat de support Microsoft pour ouvrir une demande lorsque la ressource n’est pas disponible suite à des événements de plateforme.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Resource Health fait-il la différence entre une indisponibilité causée par des problèmes de plateforme et une mauvaise manipulation de l’utilisateur ?
Oui, lorsqu’une ressource n’est pas disponible, Resource Health identifie l’origine du problème selon les catégories suivantes : 
-   Action initiée par l’utilisateur
-   Événement planifié 
-   Événement non planifié

Sur le portail, les actions initiées par l’utilisateur sont affichées à l’aide d’une icône de notification bleue, tandis que les événements planifiés et non planifiés sont affichés à l’aide d’une icône d’avertissement rouge. Pour plus d’informations, voir [Vue d’ensemble d’Azure Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Puis-je intégrer Resource Health à mes outils d’analyse ?
Resource Health est un service conçu pour vous aider à diagnostiquer et à résoudre les problèmes de service Azure qui affectent vos ressources. Bien que vous puissiez utiliser l’API Resource Health pour obtenir par programme l’état d’intégrité, nous vous recommandons d’utiliser des mesures pour analyser vos ressources. Une fois qu’un problème est détecté, Resource Health vous permet d’en déterminer l’origine et vous guide au fil des actions pour le résoudre. Visitez [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pour en savoir plus sur la façon dont vous pouvez utiliser les mesures pour vérifier vos ressources.

## <a name="where-do-i-find-resource-health"></a>Où trouver Resource Health ?
Une fois connecté au portail Azure, vous pouvez accéder à Resource Health de plusieurs manières :
- Accédez à votre ressource. Dans le volet de navigation de gauche, sélectionnez **Resource Health**.
- Accédez au panneau Azure Monitor.  Dans le volet de navigation de gauche, sélectionnez **Resource Health**.
- Sélectionnez le point d’interrogation dans le coin supérieur droit du portail, puis choisissez **Aide + Support** pour ouvrir le panneau **Aide et support**. Lorsque le panneau s’ouvre, sélectionnez **Resource Health**

Vous pouvez également utiliser l’API Resource Health pour obtenir des informations sur l’intégrité de vos ressources.

## <a name="is-resource-health-available-for-all-resource-types"></a>Resource Health est-il disponible pour tous les types de ressources ?
Vous trouverez la liste des contrôles d’intégrité et des types de ressources pris en charge par Resource Health [ici](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Que dois-je faire si ma ressource apparaît comme disponible, alors que ce n’est pas le cas ?
Lors de la vérification de l’intégrité d’une ressource, cliquez sur **Rapport d’état d’intégrité incorrect** sous l’état d’intégrité. Avant d’envoyer le rapport, vous avez la possibilité de fournir des détails supplémentaires sur la raison pour laquelle vous pensez que l’état actuel est incorrect.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Resource Health est-il disponible pour toutes les régions Azure ? 
Resource Health est disponible dans toutes les zones Azure, à l’exception des régions suivantes :
- Gouvernement américain - Virginie
- US Gov Iowa
- Est des États-Unis – US DoD
- Centre des États-Unis – US DoD
- Centre de l’Allemagne
- Nord-Est de l’Allemagne

## <a name="how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications"></a>En quoi Resource Health diffère-t-il du tableau de bord d’état du service ou des notifications de service de portail Azure ?
Les informations fournies par Resource Health sont plus précises que celles du tableau de bord d’état du service Azure.

Tandis que le [statut Azure](https://status.azure.com) et les notifications de service de portail vous informent sur les problèmes de service affectant un large éventail de clients (par exemple, une région Azure), Resource Health expose des événements plus précis qui ne concernent que la ressource spécifique. Par exemple, si un hôte redémarre inopinément, Resource Health n’avertit que les clients dont les machines virtuelles étaient exécutées sur cet hôte.

Pour pouvoir vous fournir une visibilité complète des événements ayant un impact sur vos ressources, Resource Health expose également les événements publiés dans les notifications de service et dans le tableau de bord d’état du service.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Dois-je activer Resource Health pour chaque ressource ?
Non, les informations d’intégrité sont disponibles pour tous les types de ressources disponibles via Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Faut-il activer Resource Health pour mon organisation ?
Non.  Azure Resource Health est accessible via le portail Azure sans conditions requises d’installation.

## <a name="is-resource-health-available-free-of-charge"></a>Resource Health est-il disponible gratuitement ?
Oui.  Azure Resource Health est gratuit.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quelles sont les recommandations que fournit Resource Health ?
En fonction de l’état d’intégrité, Resource Health fournit des recommandations dans le but de réduire le temps passé à résolution les problèmes. Pour les ressources disponibles, les recommandations sont axées sur la façon de résoudre les principaux problèmes rencontrés par les clients. Si la ressource est indisponible en raison d’un événement Azure non planifié, l’objectif principal est de vous assister pendant et après le processus de récupération. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur Resource Health :
-  [Vue d’ensemble d’Azure Resource Health](Resource-health-overview.md)
-  [Types de ressource et contrôles d’intégrité disponibles par le biais d’Azure Resource Health](resource-health-checks-resource-types.md)
