---
title: Guide de dépannage d’Azure Mobile Engagement - Push/Reach
description: Résolution des problèmes des interactions et des notifications utilisateur dans Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3f1886b7-1fdd-47f4-b6b0-d79f158d5ef3
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bc94353e60288ba1acfb1855b3a7b416a03e73bc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Guide de dépannage pour les problèmes liés à Push et Reach
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Voici des problèmes potentiels liés à la façon dont Azure Mobile Engagement envoie des informations à vos utilisateurs.

## <a name="push-failures"></a>Échecs Push
### <a name="issue"></a>Problème
* Push ne fonctionne pas (dans l'application, hors de l'application, ou les deux).

### <a name="causes"></a>Causes
* Un échec push indique souvent qu'Azure Mobile Engagement, Reach ou une autre fonctionnalité avancée d'Azure Mobile Engagement n'est pas correctement intégrée ou qu'une mise à niveau est requise dans le Kit de développement logiciel (SDK) pour résoudre un problème connu avec une nouvelle plate-forme de système d'exploitation ou de périphérique.
* Testez simplement un push dans l'application et un push hors de l'application pour déterminer si le problème se situe dans l'application ou hors de l'application.
* Dans le cadre du dépannage, effectuez des tests à partir de l'interface utilisateur et à partir de l'API pour voir les informations d'erreur supplémentaires disponibles aux deux emplacements.
* Les push en dehors de l'application ne fonctionneront pas si Azure Mobile Engagement et Reach sont intégrés dans le Kit de développement logiciel (SDK).
* Les transmissions de type push ne fonctionneront pas si les certificats ne sont pas valides, ou s’ils utilisent PROD DEV correctement (iOS uniquement). (**Remarque :** les notifications Push « en dehors de l’application » ne sont pas remises à iOS, si les versions de développement (DEV) et de production (PROD) de votre application sont installées sur le même périphérique, dans la mesure où le jeton de sécurité associé à votre certificat peut être invalidé par Apple. Pour résoudre ce problème, désinstallez les versions PROD et DEV de votre application et ré-installez uniquement une version sur votre périphérique.)
* Les décomptes de push en dehors de l'application sont gérés différemment sur des plates-formes différentes (iOS affiche moins d'informations qu'Android si les push natifs sont désactivés sur un périphérique, l'API peut fournir davantage d'informations que l'interface utilisateur dans les statistiques de push).
* Les push en dehors de l'application peuvent être bloqués par les clients au niveau du système d'exploitation (iOS et Android).
* Les push en dehors de l'application apparaissent comme désactivés dans l'interface utilisateur d'Azure Mobile Engagement s'ils ne sont pas correctement intégrés, mais peuvent échouer en mode silencieux à partir de l'API.
* Les push dans l'application ne fonctionneront pas si Azure Mobile Engagement et Reach sont intégrés dans le Kit de développement logiciel (SDK).
* Les push GCM et ADM ne fonctionneront pas si Azure Mobile Engagement et le serveur spécifique sont intégrés dans le Kit de développement logiciel (Android uniquement).
* Les push dans l'application et en dehors de l'application doivent être testés séparément pour déterminer s'il s'agit d'un problème Push ou Reach.
* Les push dans l'application requièrent que l'application soit ouverte pour être reçue.
* Les push dans l'application sont souvent configurés pour être filtrés par une balise d'informations d'application d'inclusion ou d'exclusion.
* Si vous utilisez une catégorie personnalisée dans Reach pour afficher les notifications dans l'application, vous devez suivre le cycle de vie correct de la notification, sans quoi la notification pourrait ne pas être effacée lorsque l'utilisateur la fait disparaître.
* Si vous lancez une campagne sans date de fin et qu'un périphérique reçoit la notification dans l'application mais ne l'affiche pas encore, l'utilisateur continuera à recevoir la notification lors de sa prochaine connexion à l'application, même si vous arrêtez manuellement la campagne.
* Pour les problèmes avec l'API Push, confirmez que vous souhaitez vraiment utiliser l'API Push au lieu de l'API Reach (l'API Reach étant utilisée plus souvent) et que vous ne confondez pas les paramètres « charge utile » et « notificateur ».
* Testez votre campagne push avec à la fois un périphérique connecté via le Wi-Fi et 3G pour éliminer la connexion réseau comme source potentielle de problèmes.

## <a name="push-testing"></a>Test push
### <a name="issue"></a>Problème
* Les push peuvent être envoyés à un périphérique spécifique basé sur un ID de périphérique.

### <a name="causes"></a>Causes
* Appareils de test sont configurés différemment pour chaque plate-forme, mais à l'origine d'un événement dans votre application sur un périphérique de test et recherchez votre ID de périphérique dans le portail doivent fonctionner pour trouver votre ID de périphérique pour toutes les plateformes.
* Les périphériques de test fonctionnent différemment avec IDFA et IDFV (iOS uniquement).

## <a name="push-customization"></a>Personnalisation push
### <a name="issue"></a>Problème
* L'élément de contenu push avancé ne fonctionne pas (badge, sonnerie, vibration, image, etc.).
* Les liens de push ne fonctionnent pas (hors de l'application, dans l'application, vers un site Web, à un emplacement dans l'application).
* Les statistiques de push indiquent qu'un push n'a pas été envoyé au nombre de personnes prévu (trop ou trop peu).
* Push dupliqués et reçus en double.
* Impossible d'inscrire le dispositif de test pour les push Azure Mobile Engagement (avec votre propre application de production ou développement).

### <a name="causes"></a>Causes
* Pour créer un lien vers un emplacement spécifique dans l'application requiert "categories" (Android uniquement).
* Des schémas de lien profond pour rediriger les utilisateurs vers un autre emplacement après avoir cliqué sur une notification Push doivent être créés dans votre application et le système d’exploitation du périphérique et gérés par ces derniers, et non par Mobile Engagement directement. (**Remarque :** les notifications en dehors de l’application ne peuvent pas lier directement à des emplacements au sein de l’application avec iOS, contrairement à Android.)
* Les serveurs d'images externes doivent être en mesure d'utiliser HTTP « GET » et « HEAD » pour que les push de grandes images fonctionnent (Android uniquement).
* Dans votre code, vous pouvez désactiver l'agent Azure Mobile Engagement lorsque le clavier est ouvert et que votre code réactive l'agent Azure Mobile Engagement une fois que le clavier est fermé afin que le clavier n'affecte pas l'apparence de votre notification (iOS uniquement).
* Certains éléments ne fonctionnent pas dans les simulations de test, mais uniquement les campagnes réelles (badge, sonnerie, vibration, image, etc.).
* Aucune donnée du côté serveur n'est enregistrée lorsque vous utilisez le bouton pour « tester » les push. Les données sont enregistrées uniquement pour les campagnes push réelles.
* Pour aider à isoler le problème, résolvez les problèmes avec : tester, simuler et une campagne réelle puisque chacun d'eux fonctionne légèrement différemment.
* La durée pendant laquelle vos campagnes « dans l’application » et « à tout moment » sont planifiées pour s’exécuter affecte les numéros de livraison, dans la mesure où une campagne n’est remise qu’aux utilisateurs « dans l’application » pendant son exécution (et aux utilisateurs disposant de paramètres de périphérique définis pour recevoir des notifications « en dehors de l’application »).
* Les différences de gestion des notifications en dehors de l’application entre Android et iOS compliquent la comparaison directe des statistiques de transmission entre la version Android et iOS de votre application. Android fournit plus d’informations de notification au niveau du système d’exploitation qu’iOS. Android signale dans le centre de notification lorsqu’une notification native est reçue, supprimée ou qu’un utilisateur clique dessus, mais iOS ne signale pas ces informations, à moins que l’utilisateur clique sur la notification. 
* La principale raison pour laquelle les statistiques de « push » sont différentes de celles de « livraison » pour campagnes Reach est que les notifications « dans l’application » et « en dehors de l’application » sont comptées différemment. Les notifications « dans l’application » sont gérées par Mobile Engagement, tandis que les notifications « en dehors de l’application » sont gérées par le centre de notification du système d’exploitation de votre périphérique.

## <a name="push-targeting"></a>Ciblage push
### <a name="issue"></a>Problème
* La ciblage intégré ne fonctionne pas comme prévu.
* Le ciblage de balise d'informations d'application ne fonctionne pas comme prévu.
* Le ciblage par géolocalisation ne fonctionne pas comme prévu.
* Les options de langue ne fonctionnent pas comme prévu.

### <a name="causes"></a>Causes
* Assurez-vous que vous avez téléchargé les balises d'informations d'applications via l'interface utilisateur ou l'API Azure Mobile.
* La limitation de la vitesse des push ou le quota de push au niveau de l'application, ou la limitation de l'audience au niveau de la campagne peut empêcher une personne de recevoir un push spécifique, même si elle répond à vos critères de ciblage. 
* La définition d'une « langue » est différente du ciblage en fonction du pays ou des paramètres régionaux, qui lui-même est différent du ciblage en fonction de l'emplacement géographique basé sur l'emplacement téléphonique ou du GPS.
* Le message dans la « langue par défaut » est envoyé à tous les clients dont le périphérique n'est pas défini sur l'une des langues alternatives que vous spécifiez.

## <a name="push-scheduling"></a>Planification push
### <a name="issue"></a>Problème
* La planification push ne fonctionne pas comme prévu (envoi trop précoce ou retardé).

### <a name="causes"></a>Causes
* Fuseaux horaires peuvent problèmes grâce à la planification, surtout lorsque vous utilisez le fuseau horaire de l'utilisateur final.
* Les fonctionnalités push avancées peuvent retarder les push.
* Le ciblage en fonction des paramètres du téléphone (au lieu des balises d'informations de l'application) peut retarder les push car Azure Mobile Engagement peut avoir à demander les données du téléphone en temps réel avant d'envoyer un push.
* Les campagnes marketing créées sans date de fin stockent le push localement sur le périphérique et l'affichent lors de la prochaine ouverture de l'application, même si la campagne est terminée manuellement.
* Le démarrage simultané de plusieurs campagnes peut mettre plus de temps à analyser votre base d'utilisateurs (essayez de démarrer une seule campagne à la fois avec un maximum de quatre, et ciblez uniquement vos utilisateurs actifs afin que les anciens utilisateurs n'aient pas à être analysés).
* Si vous utilisez l'option « Ignorer l'audience, la notification Push sera envoyée aux utilisateurs via l'API Reach » dans la section « Campagne » de la campagne Reach, cette campagne ne sera pas automatiquement envoyée. Vous devrez l'envoyer manuellement via l'API Reach.
* Si vous utilisez une catégorie personnalisée dans Reach pour afficher les notifications dans l'application, vous devez suivre le cycle de vie correct d'une notification, sans quoi la notification pourrait ne pas être effacée lorsque l'utilisateur la fait disparaître.

