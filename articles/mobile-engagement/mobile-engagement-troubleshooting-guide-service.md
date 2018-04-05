---
title: Guide de dépannage d’Azure Mobile Engagement - Service
description: Guides de dépannage pour Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 64bd4849e3bb9a37fb32940548d5ecf01fffe0ca
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-service-issues"></a>Guide de résolution des problèmes de service
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Les éléments suivants sont des problèmes potentiels liés à l’exécution d’Azure Mobile Engagement.

## <a name="service-outages"></a>Interruptions de service
### <a name="issue"></a>Problème
* Problèmes qui semblent provenir des interruptions de service d’Azure Mobile Engagement.

### <a name="causes"></a>Causes
* Problèmes qui semblent provenir d'Azure Mobile Engagement des interruptions de Service peuvent être dû à plusieurs problèmes différents :
  * Problèmes isolés systémiques à l'origine sur l'ensemble d'Azure Mobile Engagement
  * Problèmes connus provoqués par des pannes de serveur (ne s'affichent pas toujours dans l'état du serveur) :
  * Retards de planification, erreurs de ciblage, problèmes de mise à jour de Badge, arrêt de la collecte des statistiques, arrêt du fonctionnement des notifications Push, arrêt du fonctionnement des API, impossibilité de créer des applications ou des utilisateurs, erreurs DNS et erreurs de délai d’attente dans l’interface utilisateur, les API ou les applications sur un périphérique.
  * Interruptions de dépendance de cloud [État du Service Azure](http://status.azure.com/)
  * Interruptions de dépendance des Services de notification push (PNS)
  * Interruptions App Store

1) Pour savoir si le problème est lié au système, vous pouvez tester la même fonction à partir d’une autre

* Application intégrée d’Azure Mobile Engagement
* Appareil de test
* Version du système d’exploitation de l’appareil de test
* Campagne
* Compte utilisateur de l’administrateur
* Navigateur (Internet Explorer, Firefox, Chrome, etc.)
* Ordinateur

2) Pour savoir si le problème affecte uniquement l'interface utilisateur ou les API :

* Testez la même fonction à partir de l’interface utilisateur d’Azure Mobile Engagement et l’API d’Azure Mobile Engagement.

3) Pour tester si le problème provient du réseau de votre téléphone cellulaire :

* Testez quand vous êtes connecté à Internet via le Wi-Fi et quand vous êtes connecté via le réseau de votre téléphone portable 3G de test.
* Confirmez que votre pare-feu ne bloque pas les ports ni les adresses IP Azure Mobile Engagement.

4) Pour tester si le problème provient de votre périphérique :

* Testez si votre appareil est en mesure de se connecter à Azure Mobile Engagement avec une autre application intégrée Azure Mobile Engagement.
* Testez que vous pouvez générer des événements, des travaux et des incidents à partir de votre téléphone, qui peuvent être consultés dans l’interface utilisateur d’Azure Mobile Engagement. 
* Vérifiez si vous pouvez envoyer des notifications push à partir de l’interface utilisateur d’Azure Mobile Engagement sur votre appareil en fonction de son ID d’appareil. 

5) Pour tester si le problème provient de votre application :

* Installez et testez votre application à partir d’un émulateur plutôt qu’à partir d'un appareil physique :

6) Pour tester si le problème avec les mises à niveau du système d'exploitation à l'utilisateur final les périphériques qui nécessitent une mise à niveau du Kit de développement logiciel pour résoudre :

* Testez votre application sur différents appareils avec différentes versions du système d’exploitation.
* Confirmez que vous utilisez la version la plus récente du Kit de développement logiciel (SDK).

## <a name="connectivity-and-incorrect-information-issues"></a>Problèmes de connectivité et informations incorrectes
### <a name="issue"></a>Problème
* Problèmes de connexion à l'interface utilisateur d'Azure Mobile Engagement.
* Erreurs de connexion avec l'API d'Azure Mobile Engagement.
* Problèmes lors du chargement des balises d'informations d'application via l'API de l'appareil.
* Problèmes lors du téléchargement des journaux ou des données exportées à partir d'Azure Mobile Engagement.
* Informations incorrectes indiquées dans l'interface utilisateur de Azure Mobile Engagement.
* Informations incorrectes indiquées dans les journaux Azure Mobile Engagement.

### <a name="causes"></a>Causes
* Confirmez que votre compte d'utilisateur dispose des autorisations nécessaires pour effectuer la tâche.
* Vérifiez que le problème n'est pas isolé sur un seul ordinateur ou votre réseau local.
* Vérifiez que que le service Azure Mobile Engagement n'a pas signalé d'interruptions.
* Vérifiez que vos fichiers de balise d'informations d'application respectent toutes ces règles :
  * Utilisez uniquement le jeu de caractères UTF8 (le jeu de caractères ANSI n'est pas pris en charge).
  * Utilisez une virgule « , » comme caractère de séparation (vous pouvez ouvrir une demande de service pour modifier le caractère de séparation .csv afin que ce ne soit plus une virgule (« , ») mais un autre caractère, par exemple un point-virgule « ; »).
  * Utilisez des minuscules pour les valeurs booléennes « true » et « false ».
  * Utilisez un fichier d'une taille inférieure à la taille maximale de 35 Mo.

