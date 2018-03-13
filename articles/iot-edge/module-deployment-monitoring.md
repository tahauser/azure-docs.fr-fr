---
title: "Déployer des modules pour Azure IoT Edge | Microsoft Docs"
description: "En savoir plus sur la façon dont les modules sont déployés sur les appareils périphériques"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0fb8c55937c1f4c29c542204673a2f41e3ae29db
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle - Aperçu

Les appareils Azure IoT Edge suivent un [cycle de vie des appareils] [ lnk-lifecycle] similaire à d’autres types d’appareils IoT :

1. Les appareils IoT Edge sont approvisionnés, ce qui implique de créer un appareil doté d’un système d’exploitation et d’installer le [runtime IoT Edge][lnk-runtime].
1. Les périphériques sont configurés pour exécuter [les modules IoT Edge][lnk-modules] puis contrôlés pour l’intégrité. 
1. Enfin, les périphériques peuvent être mis hors-service lorsqu’ils sont remplacés ou deviennent obsolètes.  

Azure IoT Edge propose deux façons de configurer les modules à exécuter sur les appareils IoT Edge : une pour le développement et les itérations rapides sur un appareil unique (que vous avez utilisé dans les didacticiels Azure IoT Edge), et une pour la gestion des fleets volumineux des appareils IoT Edge. Ces deux approches sont disponibles dans le portail Azure et par programme.

Cet article se concentre sur la configuration et la surveillance des étapes pour les flottes des périphériques, collectivement appelées des déploiements IoT Edge. Les étapes de déploiement global sont les suivantes :   

1. Un opérateur définit un déploiement qui décrit un ensemble de modules, ainsi que les appareils cibles. Chaque déploiement possède un manifeste de déploiement qui reflète ces informations. 
1. Le service de IoT Hub communique avec tous les appareils ciblés pour les configurer avec les modules souhaités. 
1. Le service de IoT Hub récupère l’état à partir des appareils IoT Edge et l’expose pour l’opérateur vers le moniteur.  Par exemple, un opérateur voit quand un appareil Edge n’est pas correctement configuré ou si un module échoue durant l’exécution. 
1. À tout moment, les nouveaux appareils IoT Edge qui remplissent les conditions de ciblage sont configurés pour le déploiement. Par exemple, un déploiement qui cible tous les appareils IoT Edge dans l’état de Washington configure automatiquement un nouvel appareil IoT Edge une fois qu’il est approvisionné et ajouté au groupe des périphériques de l’état de Washington. 
 
Cet article définit chaque composant impliqué dans la configuration et la surveillance d’un déploiement. Pour connaître la procédure de création et de mise à jour d’un déploiement, consultez [Déployer et surveiller des modules IoT Edge à l’échelle][lnk-howto].

## <a name="deployment"></a>Déploiement

Un déploiement affecte aux modules IoT Edge des images à exécuter en tant qu’instances sur un ensemble ciblé de périphériques IoT Edge. Il fonctionne en configurant un manifeste de déploiement IoT Edge pour inclure une liste de modules comprenant les paramètres d’initialisation correspondants. Un déploiement peut être affecté à un périphérique unique (généralement basé sur l’ID de l’appareil) ou à un groupe d’appareils (basé sur des balises). Une fois qu’un périphérique IoT Edge reçoit un manifeste de déploiement, il télécharge et installe les images conteneurs du module à partir des référentiels conteneurs respectifs, et les configure en conséquence. Une fois qu’un déploiement est créé, un opérateur peut surveiller l’état de déploiement pour voir si les appareils ciblés sont configurés correctement.   

Les périphériques doivent être approvisionnés en tant qu’appareils IoT Edge pour être configurés avec un déploiement. Les éléments suivants sont des composants requis et ne sont pas inclus dans le déploiement :
* Le système d’exploitation de base
* Docker 
* L’approvisionnement du runtime IoT Edge 

### <a name="deployment-manifest"></a>Manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules devant être configurés sur les appareils IoT Edge ciblés. Il contient les métadonnées de configuration pour tous les modules, y compris les modules système requis (en particulier l’agent de IoT Edge et l’hub IoT Edge).  

Les métadonnées de configuration pour chaque module incluent : 
* Version 
* type 
* État (par exemple, en cours d’exécution ou arrêté) 
* Stratégie de redémarrage 
* Référentiel d’image et de conteneur 
* Itinéraires pour les données en entrée et en sortie 

### <a name="target-condition"></a>Condition cible

La condition cible est évaluée en permanence pour inclure les nouveaux appareils qui répondent aux exigences ou pour supprimer les appareils qui n’y répondent plus tout au long de la durée de vie du déploiement. Celui-ci sera réactivé si le service détecte une modification de la condition cible. Prenons l’exemple d’un déploiement A comportant la condition cible tags.environment = ’prod’. Au lancement du déploiement, il existe 10 appareils de production. Les modules sont correctement installés sur ces 10 appareils. L’état de l’Agent IoT Edge affiche 10 appareils au total, 10 réponses correctes, 0 échecs de réponse et 0 réponses en attente. On ajoute maintenant 5 appareils avec tags.environment = ’prod’. Le service détecte la modification, et l’état de l’Agent IoT Edge devient : 15 appareils au total, 10 réponses correctes, 0 échecs de réponses et 5 réponses en attente lorsqu’il tente de déployer les cinq nouveaux appareils.

Utilisez une condition booléenne sur des balises des jumeaux d’appareils ou deviceId pour sélectionner les appareils cibles. Si vous souhaitez utiliser une condition avec des balises, vous devez ajouter les propriétés "tags":{} dans le jumeau d’appareil au même niveau. [En savoir plus sur les balises dans le jumeau d’appareil](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

Exemples de conditions cibles :
* deviceId =’linuxprod1’
* tags.environment =’prod’
* tags.environment = ’prod’ AND tags.location = ’westus’
* tags.environment = ’prod’ OR tags.location = ’westus’
* tags.operator = ’John’ AND tags.environment = ’prod’ NOT deviceId = ’linuxprod1’

Voici quelques-unes des contraintes qui s’appliquent à la création d’une condition cible :

* Dans le jumeau d’appareil, seuls les balises et deviceId permettent de créer une condition cible.
* Les guillemets doubles ne sont autorisés nulle part dans la condition cible. Utilisez des guillemets simples.
* Les guillemets simples représentent les valeurs de la condition cible. Par conséquent, vous devez échapper le guillemet simple avec un autre guillemet simple s’il fait partie du nom de l’appareil. Par exemple, la condition cible de operator’sDevice s’écrit deviceId = ’operator’’sDevice’.
* Les nombres, les lettres et les caractères suivants sont autorisés dans les valeurs de la condition cible : -:.+%_#*?!(),=@;$.

### <a name="priority"></a>Priorité

Une priorité définit si un déploiement doit être appliqué à un appareil ciblé par rapport à d’autres déploiements. Une priorité de déploiement est un entier positif. Plus le nombre est élevé, plus la priorité est supérieure. Si un périphérique IoT Edge est ciblé par plusieurs déploiements, le déploiement avec la priorité la plus élevée s’applique.  Les déploiements avec une priorité plus faible ne sont pas appliqués, ni fusionnés.  Si un périphérique est ciblé par au moins deux déploiements de priorité égale, c’est le déploiement créé le plus récemment (déterminé par l’horodatage de création) qui s’applique.

### <a name="labels"></a>Étiquettes 

Les étiquettes sont des paires clé-valeur de type chaîne que vous pouvez utiliser pour filtrer et grouper les déploiements. Un déploiement peut avoir plusieurs étiquettes. Les étiquettes sont facultatives et n’ont aucun impact sur la configuration des appareils IoT Edge. 

### <a name="deployment-status"></a>état du déploiement

Un déploiement peut être surveillé pour déterminer s’il est correctement appliqué pour n’importe quel appareil IoT Edge ciblé.  Un périphérique Edge ciblé apparaîtra dans une ou plusieurs des catégories d’état suivantes : 
* **Cible** affiche les périphériques IoT Edge qui correspondent à la condition de ciblage du déploiement.
* **Réel** affiche les appareils IoT Edge ciblés qui ne sont pas ciblés par un autre déploiement de priorité plus élevée.
* **Intègre** affiche les appareils IoT Edge ayant signalé au service que les modules ont été déployés correctement. 
* **Défectueux** affiche les appareils IoT Edge ayant signalé au service qu’un ou plusieurs modules n’ont pas été déployés correctement. Pour examiner l’erreur en détail, vous devez vous connecter à distance aux appareils en question et consulter les fichiers journaux.
* **Inconnu** affiche les appareils IoT Edge qui n’ont pas signalé d’état concernant le déploiement. Pour approfondir vos recherches, consultez les informations de service et les fichiers journaux.

## <a name="phased-rollout"></a>Déploiement progressif 

Un déploiement progressif est un processus global par lequel un opérateur déploie les modifications sur un ensemble étendu d’appareils IoT Edge. L’objectif est d’apporter des modifications progressivement afin de réduire le risque d’étendre les modifications avec rupture à une plus grande échelle.  

Un déploiement progressif est exécuté dans les phases et étapes suivantes : 
1. Établissez un environnement de tests d’appareils IoT Edge en les approvisionnant et en paramétrant une balise de jumeau d’appareil comme `tag.environment='test'`. L’environnement de test doit refléter l’environnement de production que le déploiement va cibler. 
1. Créez un déploiement comprenant les modules et les configurations souhaités. La condition de ciblage doit cibler l’environnement de test des appareils IoT Edge.   
1. Validez la nouvelle configuration du module dans l’environnement de test.
1. Mettez à jour le déploiement pour inclure un sous-ensemble d’appareils de production IoT Edge en ajoutant une nouvelle balise à la condition de ciblage. En outre, assurez-vous que la priorité pour le déploiement est supérieure aux autres déploiements actuellement ciblés sur ces appareils. 
1. Vérifiez que le déploiement a réussi sur les appareils IoT ciblés en consultant l’état du déploiement.
1. Mettez à jour le déploiement afin de cibler tous les appareils de production IoT Edge restants.

## <a name="rollback"></a>Restauration

Les déploiements peuvent être restaurés en cas d’erreur ou de mauvaise configuration.  Étant donné qu’un déploiement définit la configuration de module absolue pour un appareil IoT Edge, un déploiement supplémentaire doit cibler le même appareil à une priorité inférieure, même si l’objectif est de supprimer tous les modules.  

Effectuez des restaurations dans l’ordre suivant : 
1. Confirmez qu’un deuxième déploiement cible également le même ensemble d’appareils. Si l’objectif de la restauration est de supprimer tous les modules, le deuxième déploiement ne doit pas contenir de module. 
1. Modifiez ou supprimez l’expression de la condition cible du déploiement que vous souhaitez restaurer de façon à ce que les appareils ne répondent pas à la condition de ciblage.
1. Vérifiez que la restauration a réussi en consultant l’état du déploiement.
   * Le déploiement restauré ne doit plus afficher d’état pour les appareils qui ont été restaurés.
   * Le deuxième déploiement doit désormais inclure l’état de déploiement pour les appareils qui ont été restaurés.


## <a name="next-steps"></a>Étapes suivantes

* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller les modules IoT Edge à l’échelle][lnk-howto].
* En savoir plus sur d’autres concepts IoT Edge tels que le [runtime IoT Edge][lnk-runtime] et [les modules IoT Edge][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md